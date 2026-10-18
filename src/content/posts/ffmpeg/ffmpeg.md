---
title: ffmpeg的使用
published: 2026-08-15
pinned: false
description: ffmpeg的使用
tags: [ffmpeg, gif]
image: "api"
category: stack
slug: ffmpeg-use
---

## 视频转gif

```shell
ffmpeg -i input.mp4 -vf "fps=7,scale=-1:-1,split[s0][s1];[s0]palettegen=max_colors=128[p];[s1][p]paletteuse=dither=none" -loop 0 output.gif
```

将喜欢的视频转换成gif，就能在其他地方使用了。比如 Windows-Terminal 😊

![image-20260815090625660](http://imgbed.alexmaodali.dpdns.org/file/default-imgbed/1786756005057_image-20260815090625660.png)

## 视频bgm提取

```shell
ffmpeg -i input.mp4 -vn -c:a libmp3lame -q:a 2 output.mp3
```

