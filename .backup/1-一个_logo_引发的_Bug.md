[一个 logo 引发的 Bug](https://github.com/AndySuen/public-notes/issues/1)

## 1. 问题：

前端发现一个图片兼容性问题：测试环境某个门店的 logo 在 Android 设备上能正常显示，在 iOS 设备上无法显示。

图片地址：https://i.loli.net/2021/06/17/bFZoMEpgDB74kN1.jpg

前端表示，只有测试环境这个图片有问题，正式环境没有此类情况。

## 2. 问题复现

将该图片下载到本地，macOS 也无法预览。怀疑是图片文件格式已损坏。

<img src="https://i.loli.net/2021/06/17/ysMmd6bv7tzwEcp.png" alt="img" width="300px" />

## 3. 分析

通过二进制打开，发现 photoshop 编辑过的痕迹

<img src="https://i.loli.net/2021/06/17/egiduGzYwDm9o1X.png" alt="img" width="500px" />

于是尝试通过 photoshop 打开，打开成功。导出 jpeg 格式，可以正常打开。

<img src="https://i.loli.net/2021/06/17/FxGsM7CEJfztDIv.png" alt="image" width="150px" />

对比两者不同，发现导出的版本，头部多了一段二进制标签 `FFE000104A46494600010101004800480000`。

<img src="https://i.loli.net/2021/06/17/uWLqgo1ZrxyE7j5.png" alt="img" width="500px" />

查阅 [jpeg 语法结构](https://en.wikipedia.org/wiki/JPEG#Syntax_and_structure) 和 [jpeg 文件格式](https://en.wikipedia.org/wiki/JPEG_File_Interchange_Format)，发现此段为 JFIF APP0 marker segment，应该是源文件缺失了这段信息导致无法正常打开。

| Short name | Bytes        | Payload         | Name                 | Comments                                                     |
| ---------- | ------------ | --------------- | -------------------- | ------------------------------------------------------------ |
| APP*n*     | 0xFF, 0xE*n* | *variable size* | Application-specific | For example, an Exif JPEG file uses an APP1 marker to store metadata, laid out in a structure based closely on [TIFF](https://en.wikipedia.org/wiki/TIFF). |

**JFIF APP0 marker segment:**

| Field         | Size (bytes) | Description                                                  |
| ------------- | ------------ | ------------------------------------------------------------ |
| APP0 marker   | 2            | `FF E0`                                                      |
| Length        | 2            | Length of segment excluding APP0 marker                      |
| Identifier    | 5            | `4A 46 49 46 00` = "JFIF" in [ASCII](https://en.wikipedia.org/wiki/ASCII), terminated by a null byte |
| JFIF version  | 2            | First byte for major version, second byte for minor version (`01 02` for 1.02) |
| Density units | 1            | Units for the following pixel density fields. `00` : No units; width:height [pixel aspect ratio](https://en.wikipedia.org/wiki/Pixel_aspect_ratio) = Ydensity:Xdensity,  `01` : Pixels per inch (2.54 cm),  `02` : Pixels per centimeter |
| Xdensity      | 2            | Horizontal pixel density. Must not be zero                   |
| Ydensity      | 2            | Vertical pixel density. Must not be zero                     |
| Xthumbnail    | 1            | Horizontal pixel count of the following embedded RGB thumbnail. May be zero |
| Ythumbnail    | 1            | Vertical pixel count of the following embedded RGB thumbnail. May be zero |

所以，`FFE000104A46494600010101004800480000` 这段标签的含义是

| Data         | Description                         |
| ------------ | ----------------------------------- |
| `FFE0`       | APP0 标记                           |
| `0010`       | 此段长度 `16` 字节                  |
| `4A46494600` | JFIF 的 ascii 码，加上一个 `\0`     |
| `0101`       | JFIF 版本：`1.01`                   |
| `01`         | 像素密度单位，`01` 表示 `像素/英寸` |
| `0048`       | 横向像素密度：`72`                  |
| `0048`       | 纵向像素密度：`72`                  |
| `00`         | 缩略图横向像素：`0`                 |
| `00`         | 缩略图纵向像素：`0`                 |

## 4. 修复

```
# 生成备份
cp -p bblocrdeezysaimskmluavayvhck.jpg test.jpg

# 通过 xxd 进行转储
xxd -p test.jpg test.jpg.txt

vim test.jpg.txt
# 将缺失的信息插入原位置： FFE000104A46494600010101004800480000 并保存

# 转回二进制格式
xxd -r -p test.jpg.txt test-edited.jpg
```

编辑后的 test-edited.jpg 可以正常预览

<img src="https://i.loli.net/2021/06/17/ngDPYhWUipXLeMb.png" alt="image" width="300px" />

将该文件上传，替换原门店 logo，在 iOS 上可以正常显示了。

问题解决。

## 5. 为什么不直接换个差不多的图片直接上传呢？

🤔

嗯……是个好问题

## P.S. 产品说他这个源文件是他截图得到的

<img src="https://i.loli.net/2021/06/17/E5A6lvcqfQoY1hB.jpg" alt="img" width="300px" />

看来截图工具没有按标准实现

微信🌶️🐔

(完)
