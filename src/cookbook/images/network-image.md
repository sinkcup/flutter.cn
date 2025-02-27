---
title: Display images from the internet
title: 显示网络上的远程图片
description: How to display images from the internet.
description: 如何显示网络上的图片。
tags: cookbook, 实用教程, 使用图片
keywords: 网络图片,使用gif
js:
  - defer: true
    url: https://dartpad.cn/inject_embed.dart.js
---

<?code-excerpt path-base="cookbook/images/network_image"?>

Displaying images is fundamental for most mobile apps.
Flutter provides the [`Image`][] widget to
display different types of images.

对大多数移动应用来说，图片显示是一项基础功能。
Flutter 提供了 [`Image`][] 来显示不同类型的图片。

To work with images from a URL, use the
[`Image.network()`][] constructor.

使用 [`Image.network()`][] 构造函数来处理来自 URL 的图片。

<?code-excerpt "lib/main.dart (ImageNetwork)" replace="/^body\: //g"?>
```dart
Image.network('https://picsum.photos/250?image=9'),
```

## Bonus: animated gifs

## 意外之喜：Gif 动画

One useful thing about the `Image` widget:
It supports animated gifs.

`Image` widget 令人兴奋的特性之一：提供了开箱即用的 gif 动画支持！

<?code-excerpt "lib/gif.dart (Gif)" replace="/^return\ //g"?>
```dart
Image.network(
    'https://docs.flutter.dev/assets/images/dash/dash-fainting.gif');
```

## Placeholders and caching

## 占位符与缓存

The default `Image.network` constructor doesn't handle more advanced
functionality, such as fading images in after loading, or caching images
to the device after they're downloaded. To accomplish these tasks, see
the following recipes:

默认的 `Image.network` 构造函数并没有提供诸如加载后的图片淡入或
下载之后将图片缓存到设备等更进一步的功能。
请参阅以下链接来实现这些功能：

* [Fade in images with a placeholder][]
  
  [占位符和网络图片淡入][Fade in images with a placeholder]
  
* [Work with cached images][]
  
  [使用缓存图片][Work with cached images]

## Interactive example

## 交互式样例

<?code-excerpt "lib/main.dart"?>
```run-dartpad:theme-light:mode-flutter:run-true:width-100%:height-600px:split-60:ga_id-interactive_example
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    var title = 'Web Images';

    return MaterialApp(
      title: title,
      home: Scaffold(
        appBar: AppBar(
          title: Text(title),
        ),
        body: Image.network('https://picsum.photos/250?image=9'),
      ),
    );
  }
}
```

<noscript>
  <img src="/assets/images/docs/cookbook/network-image.png" alt="Network image demo" class="site-mobile-screenshot" />
</noscript>


[Fade in images with a placeholder]: {{site.url}}/cookbook/images/fading-in-images
[`Image`]: {{site.api}}/flutter/widgets/Image-class.html
[`Image.network()`]: {{site.api}}/flutter/widgets/Image/Image.network.html
[Work with cached images]: {{site.url}}/cookbook/images/cached-images
