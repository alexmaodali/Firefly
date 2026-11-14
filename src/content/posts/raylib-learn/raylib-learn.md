---
title: raylib-learn
published: 2026-10-18
pinned: true
description: raylib-learn
tags: [c, raylib, stack]
image: "api"
category: stack
slug: raylib-learn
draft: false
---

## 环境

- 编辑器：<https://royqh.net/redpandacpp/> 自带raylib库

- 视频参考: <https://www.bilibili.com/video/BV1xhKsenEVk>

raylib 官网:

- <https://github.com/raysan5/raylib>
- <https://www.raylib.com/examples.html>

## 基础窗口绘画

```
InitWindow
CloseWindow
WindowShouldClose
BeginDrawing
EndDrawing
```

基础窗口绘画

```c
#include<stdio.h>
#include<raylib.h>
int main() {
	InitAudioDevice();
	InitWindow(800, 600, "Hello-RayLib");
	while(!WindowShouldClose()) {
		BeginDrawing();
		// ...
		// 绘图代码
		// ...
		EndDrawing();
		// ...
		// 事件代码
		// ...
	}
	
	CloseWindow();
	CloseAudioDevice();
	return 0;
}
```

## raylib常见数据类型

### 坐标

```c
Vector2~Vector4
typedef struct Vector2 {
    float x;
    float y;
} Vector2;
typedef Vector4 Quatrernion;

Matrix;
```

### 颜色

```c
typedef struct Color {
	unsigned char r;
	... g, b, a
}
```

### 图像

```
Image
Texture
```

### 3d

```
Camera3D
Camera2D
```

### 音乐

```
Music
Sound
```

## 基本绘制

- 以Draw开头的函数都是绘制函数，可以绘制各种图形，圆、长方形、正方形等

```c
#include<stdio.h>
#include<time.h>
#include<stdlib.h>
#include<raylib.h>
int main() {
	srand((unsigned int)time(NULL));
	int x = 0;
	int y = 0;
	InitWindow(800, 600, "Hello-RayLib");
	while(!WindowShouldClose()) {
		BeginDrawing();
		ClearBackground(PURPLE);
#if 0
		for(int i=0;i<=10000;i++){
			x = rand() % 800;
			y = rand() % 600;
			DrawPixel(x, y, GREEN);
		}
#endif
		DrawCircle(80, 100, 100, PINK);
		EndDrawing();
	}
	return 0;
}
```

## 事件

键盘控制小球移动

```c
#include<stdio.h>
#include<raylib.h>
int main() {
	int x = 20;
	int y = 20;
	SetTargetFPS(120); // 设置游戏帧率120帧
	InitWindow(800, 600, "Hello-RayLib");
	while(!WindowShouldClose()) {
		BeginDrawing();
		
		// drawing
		ClearBackground(WHITE);
		DrawCircle(x, y, 10, PINK);
		
		EndDrawing();
		
		// envent
		if(IsKeyDown(KEY_LEFT))   { x = x - 1; }
		if(IsKeyDown(KEY_RIGHT))  { x = x + 1; }
		if(IsKeyDown(KEY_UP))     { y = y - 1; }
		if(IsKeyDown(KEY_DOWN))   { y = y + 1; }
		
		if(IsKeyDown(KEY_ESCAPE)) { break; }
	}
	return 0;
}
```

鼠标左键画圆，右键画方形

```c
#include<stdio.h>
#include<raylib.h>
int main() {
	
	SetTargetFPS(120); // 设置游戏帧率120帧
	InitWindow(800, 600, "Hello-RayLib");
	while(!WindowShouldClose()) {
		BeginDrawing();
		
		// drawing
		if(IsMouseButtonDown(MOUSE_LEFT_BUTTON)) {
			DrawCircle(GetMouseX(), GetMouseY(), 10, RED);
		}
		
		if(IsMouseButtonDown(MOUSE_RIGHT_BUTTON)) {
			DrawRectangle(GetMouseX() - 5, GetMouseY() - 5, 10, 10, GREEN);
		}
		
		EndDrawing();
		
		// envent
		
		
		if(IsKeyDown(KEY_ESCAPE)) { break; }
	}
	return 0;
	
}
```

## 图片

Texture | Image

```c
#include<stdio.h>
#include<raylib.h>

int main() {
	SetTargetFPS(120);
	InitWindow(800, 600, "Hello-RayLib");
	
	Image im = LoadImage("./res/avatar2.png");
	if (!im.data) {
		printf("LoadImage failed\n");
	}
	Texture img = LoadTextureFromImage(im);
	UnloadImage(im);
	
	while(!WindowShouldClose()) {
		BeginDrawing();
		ClearBackground(BLACK);
		DrawTexture(img, 0, 0, WHITE);
		EndDrawing();
		
		if(IsKeyDown(KEY_ESCAPE)) break;
	}
	
	UnloadTexture(img);
	CloseWindow();
	return 0;
}
```

## 音效

```c
#include<stdio.h>
#include<raylib.h>
int main() {
	InitAudioDevice();
	InitWindow(800, 600, "Hello-RayLib");
	Music raylibCountry = LoadMusicStream("./res/raylib-country.MP3");
	PlayMusicStream(raylibCountry);
	
	Sound clickSound = LoadSound("./res/click.wav");
	PlaySound(clickSound);
	
	while(!WindowShouldClose()) {
		BeginDrawing();
		// ...
		// 绘图代码
		// ...
		EndDrawing();
		// ...
		// 事件代码
		// ...
		UpdateMusicStream(raylibCountry);
		if(IsMouseButtonDown(MOUSE_LEFT_BUTTON)) { PlaySound(clickSound); }
	}
	
	CloseWindow();
	CloseAudioDevice();
	return 0;
}
```

