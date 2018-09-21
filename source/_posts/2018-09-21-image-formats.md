---
layout: post
title: "Image 格式"
category: "server"
tags: ["server", "nginx"]
---

### 文件标识

| 格式 | 开头HEX | 开头字节 | 结束HEX |
|:-----|:-------|:--------|:--------| 
| jpg | FF D8 FF DB | ÿØÿÛ  |FF D9  |
| jpg | FF D8 FF E0 00 10 4A 46 49 46 00 01 | ÿØÿà..JFIF.. |FF D9  |
| jpg | FF D8 FF E1 ?? ?? 45 78 69 66 00 00 | ÿØÿá..Exif.. |FF D9  |
| png | 89 50 4E 47 0D 0A 1A 0A | .PNG.... | - |
| gif | 47 49 46 38 37 61 | GIF87a | - |
| gif | 47 49 46 38 39 61 | GIF89a | - |
| bmp | 42 4D | BM | - |
| tiff | 49 49 2A 00  | II*. | - |
| tiff | 4D 4D 00 2A | MM.* | - |
| webp | 52 49 46 46 ?? ?? ?? ?? 57 45 42 50 |  RIFF....WEBP      | - |
| ico |  00 00 01 00  | .... | - |

更多请参考 [
List of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)