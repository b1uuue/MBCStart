# Mobile Browser Capture Guide

<!-- MarkdownTOC -->

- [Instructions](#instructions)
    + [Browser Support](#browser-support)
    + [Basic Features](#basic-features)
- [Install](#install)
    + [Download Source Files](#download-source-files)
    + [Via CDN](#via-cdn)
    + [Via Package Manager](#via-package-manager)
- [Usages](#usages)
    + [Drag Images into the Viewer](#drag-images-into-the-viewer)
    + [Bind Commands to Buttons](#bind-commands-to-buttons)
    + [Start Editing Images](#start-editing-images)
    + [Bind Thumbnail Images](#bind-thumbnail-images)
    + [video](#video)
    + [Free Transform](#free-transform)
- [Gesture](#gesture)
- [Expand](#expand)
    + [pdf support](#pdf-support)
    + [tiff support](#tiff-support)
    + [wasm](#wasm)
- [Documents](#documents)
- [License](#license)

<!-- /MarkdownTOC -->

## Instructions

MBC(Mobile Browser Capture) 是一个具有比较强大功能的图片编辑器，针对移动端的做了特别的定制（手势，UI etc.）

### Browser Support

|       Chrome       |      Firefox       |       Safari       |        Edge        | Internet Explorer |
| :----------------: | :----------------: | :----------------: | :----------------: | :---------------: |
| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |        9+         |

### Basic Features

提供图片的查看、旋转翻转、剪切、变形、添加笔触、保存下载等功能

- Photo manipulation
    + Rotation
    + Mirror
    + Flip
    + Crop
    + Free Transform
    + _Brush_
- Integration function
    + Image load
    + Undo
    + Redo
    + Delete
    + Save
    + Download

## Install

### Download Source Files

创建以下目录结构

```
Mobile-Browser-Capture/
├─ js/
|  ├─ jquery-3.2.1.min.js
|  └─ mbc-2.1.3.min.js
├─ css/
|  └─ kPainter.css
└─ mbc_index.html
```

其中 `mbc_index.html` 为我们的自定义页面，下载对应版本的 jQuery 文件，文件 :link:[mbc-2.1.3.min.js](https://www.keillion.site/mobileCam/js/mbc-2.1.3.min.js) 和 :link:[kPainter.css](https://www.keillion.site/mobileCam/css/kPainter.css) 可以在对应的链接下载到。

### Via CDN

在 `mbc_index.html` 中使用以下 CDN

```html
<link rel="stylesheet" href="https://www.keillion.site/mobileCam/css/kPainter.css">
<script src="https://www.keillion.site/mobileCam/js/mbc-2.1.3.min.js"></script>
```

### Via Package Manager

## Usages

### Drag Images into the Viewer

首先需要一个位置来放置我们的图片编辑器

```html
    <!-- editor page -->
    <div id="contentEditor" class="content-editor"></div>
```

再进行初始化操作

```js
    // new an MBC Object
    var painter = new MBC();

    // get DOM
    var painterDiv = painter.getHtmlElement();
    painterDiv.style.width = '100%';
    painterDiv.style.height = '100%';
    painterDiv.style.backgroundColor = 'rgba(0,0,0,0.3)';

    // fill into the Content Editor
    $('#contentEditor').append(painterDiv);
```

那在浏览器中就有了一个填充整个页面的灰色区块。

接下来，尝试着拖拽本地图片资源到已有的灰色区块内，你会发现图片已经上传成功了:laughing:

Try again！当成功拖拽多张图之后，尝试左右滑动图片，MBC 能帮你实现图片切换:innocent:

桌面版尝试左键双击放大，右键双击缩小:mag_right:移动端双指缩放:wink:

### Bind Commands to Buttons

接下来尝试另一种方式来增删 MBC viewer 中的图片，添加以下代码段，并更新页面:thinking:

```html
    <!-- add & delet images -->
    <button onclick="painter.showFileChooseWindow()">Add</button>
    <button onclick="painter.del()">Delete</button>
```

试着点击 MBC viewer 下面新出现的按钮，实现增删图片，其他命令也可以通过自定义函数的方式绑定。

接下来将教你实现一个翻页的控件，继续在 MBC viewer 下添加如下代码，

```html
    <!-- page control -->
    <button onclick="painter.changePage('f')">|<</button>
    <button onclick="painter.changePage('p')"><</button>
    <span id="pageNum">0/0</span>
    <button onclick="painter.changePage('n')">></button>
    <button onclick="painter.changePage('l')">>|</button>
    <!-- download image -->
    <br>
    <input id="ipt-downloadName" placeholder="filename to dwonload">
    <button class="btn-download" onclick="painter.download($('#ipt-downloadName').val());">Download</button>
```

在声明 `painter` 对象之后，添加如下代码，

```js
    // update the current index & total number of images
    painter.onNumChange = function(curIndex, length){
        $("#pageNum").html((curIndex+1)+"/"+length);
    };
```

重新加载页面，就可以愉快地增删、切换不同的图片，也可以在输入框输入文件名，下载图片:wink:

### Start Editing Images

以上介绍的都是在 view mode 下生效的功能，为了方便理解，结构如下：

```
MBC Instance
  ├─ View Mode
  └─ Edit Mode
    ├─ Basic Edit Mode
    ├─ FreeTransform Mode (need `loadCvScriptAsync`)
    └─ Brush Mode
```

需要声明的是，父模式的函数在子模式下均可作用，但不一定生效:no_mouth:

For example, `FreeTransform` mode is a son mode of `Edit` mode. Function `.rotateLeft()` can still be called. However, you can't double click to zoom in or zoom out the canvas.

控制台输入 `painter.isEditing()` 返回 `false` ，说明当前是 View Mode，可知 `true` 是 Edit Mode ，其他 api 以及具体描述参见 [Documents](#documents):relieved:

接着键入以下代码，

```html
    <!-- edit image -->
    <br>
    <button onclick="painter.enterEditAsync()">Edit</button>
    <button onclick="painter.cancelEdit()">Exit Edit</button>
    <button onclick="painter.saveEditAsync()">Save</button>
    <button onclick="painter.undo()">Undo</button>
    <button onclick="painter.redo()">Redo</button>
    <button onclick="painter.rotateLeft()">Rotate Left</button>
    <button onclick="painter.rotateRight()">Rotate Right</button>
    <button onclick="painter.mirror()">Mirror</button>
    <button onclick="painter.flip()">Flip</button>
    <button onclick="painter.cropAsync()">Crop</button>
```

接下来的操作涉及 view mode 和 edit mode 的切换，首先有图状态，按下 Edit Button 进入 Edit Mode，可以看到出现了剪裁选框（可以设置 `painter.isAutoShowCropUI` 是否在 Edit Mode 下显示裁切框），运行 `painter.isEditing()` 返回 `true`。

进入 Edit Mode 之后才可以进行 `Undo`  `Redo`  `Save`  `Rotate Left`  `Rotate Right`  `Mirror`  `Flip`  `Crop` 等一系列该模式下的操作，
View Mode 下的按钮操作被屏蔽。尝试左右旋转，上下翻转，拖动剪裁框确认位置后，按下 `Crop` 按钮，`Undo` `Redo` 可以回溯操作步骤，编辑完成确认之后按下 `Save` 按钮，退出编辑模式的同时，MBC Viewer 图片队列中，在原始编辑图片之后，新增了一张刚刚编辑保存的图。

### Bind Thumbnail Images

绑定缩略图，更快捷地进行图片切换以及预览...

( click 切换/delete mode)

### video

从摄像头捕获图片，再进行编辑操作...

### Free Transform

需要导入 `cv.js` ，进行自由变换相关操作...

## Gesture

| scenario |          Desktop           |    Mobile    |
| :------: | :------------------------: | :----------: |
|   zoom   | 左键双击放大  右键双击缩小   |   两指缩放   |
|   crop   |            边框            |      ..      |
|  switch  |        单击左右滑动         | 单指左右滑动 |

- crop gesture
- free transform gesture
- brush gesture


## Expand

### pdf support

### tiff support

### wasm

## Documents

- Tutorial: [](link)
- Test: [https://www.keillion.site/mobileCam/test.html](https://www.keillion.site/mobileCam/test.html)
- Example 1: [https://www.keillion.site/mobileCam/](https://www.keillion.site/mobileCam/)
- Example 2:kissing:: [http://192.168.8.236:8062/Html/mbCreative/](http://192.168.8.236:8062/Html/mbCreative/)
- API: [https://www.keillion.site/mobileCam/js/api.md](https://www.keillion.site/mobileCam/js/api.md)

## License

:bookmark_tabs: MIT LICENSE

MBC without a license can be used for evaluation purposes. It is not stable for long time usage.

MBC need a license in production environments.




