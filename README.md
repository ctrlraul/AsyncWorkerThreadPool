# GDScript AsyncWorkerThreadPool

Wrapper for the [WorkerThreadPool](https://docs.godotengine.org/en/stable/classes/class_workerthreadpool.html) class to enable awaiting a task happening in another thread without blocking the current one.
It's recommended that you know how to use the WorkerThreadPool API to begin with in regards to what parameters do.

**NOTE: As of Godot 4.3 you have to change the setting `threading/worker_pool/max_threads` for grouped tasks to run in parallel.**

## Reason

GDScript doesn't support async IO operations by default, and I wanted to load multiple files in parallel, without causing a lag spike.

## Basic Examples
1. Loading a heavy image without causing a lag spike
```gdscript
func _ready() -> void:
	var path = "res://heavy_image.png"
	$Sprite2D.texture = await AsyncWorkerThreadPool.add_task(load_image.bind(path))

func load_image(path: String) -> Texture2D:
	var image = Image.load_from_file(path)
	return ImageTexture.create_from_image(image)
```

2. Loading multiple heavy images
```gdscript
func _ready() -> void:
	var paths = [
		"res://heavy_image_1.png",
		"res://heavy_image_2.png",
		"res://heavy_image_3.png"
	]
	
	var task = func(index: int):
		return load_image(paths[index])
	
	var heavy_textures: Array = await AsyncWorkerThreadPool.add_group_task(task)
	
	print(heavy_textures)

func load_image(path: String) -> Texture2D:
	var image = Image.load_from_file(path)
	return ImageTexture.create_from_image(image)
```
