---
title: Store key-value data on disk
title: 存储键值对数据
description: How to use the shared_preferences package to store key-value data.
description: 如何使用 shared_preferences 包来存储 key-value 数据。
tags: cookbook, 实用教程, 持久化
keywords: KV,SharedPreferences
---

<?code-excerpt path-base="cookbook/persistence/key_value/"?>

If you have a relatively small collection of key-values
to save, you can use the [`shared_preferences`][] plugin.

如果你要存储的键值集合相对较少，
则可以用 [`shared_preferences`][] 插件。

Normally,
you would have to write native platform integrations for storing
data on both iOS and Android. Fortunately,
the [`shared_preferences`][] plugin can be used to persist
key-value data on disk. The shared preferences plugin
wraps `NSUserDefaults` on iOS and `SharedPreferences` on Android,
providing a persistent store for simple data.

通常你需要在两个平台用原生的方式存储数据。
幸运的是 [`shared_preferences`] 插件可以把 key-value 保存到磁盘中。
它通过封装 iOS 上的 `NSUserDefaults` 和 Android
上的 `SharedPreferences` 为简单数据提供持久化存储。

This recipe uses the following steps:

这个教程包含以下步骤：

  1. Add the dependency.

     添加依赖

  2. Save data.

     保存数据

  3. Read data.

     读取数据

  4. Remove data.

     移除数据

{{site.alert.note}}
  To learn more, watch this short Package of the Week video on the shared_preferences package:

  <iframe class="full-width" src="{{site.youtube-site}}/embed/sa_U0jffQII" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe> 
{{site.alert.end}}

## 1. Add the dependency

## 1. 添加依赖

Before starting, add the [`shared_preferences`][] package as a dependency.

在开始之前，你需要添加 [`shared_preferences`][] 为依赖：

To add the `shared_preferences` package as a dependency,
run `flutter pub add`:

运行 `flutter pub add` 将 `shared_preferences` 添加为依赖：

```terminal
$ flutter pub add shared_preferences
```

## 2. Save data

## 2. 保存数据

To persist data, use the setter methods provided by the
`SharedPreferences` class. Setter methods are available for
various primitive types, such as `setInt`, `setBool`, and `setString`.

要存储数据，请使用 `SharedPreferences` 类的 setter 方法。
Setter方法可用于各种基本数据类型，
例如 `setInt`、`setBool` 和 `setString`。

Setter methods do two things: First, synchronously update the
key-value pair in-memory. Then, persist the data to disk.

Setter 方法做两件事：
首先，同步更新 key-value 到内存中，然后保存到磁盘中。

<?code-excerpt "lib/partial_excerpts.dart (Step2)"?>
```dart
// obtain shared preferences
final prefs = await SharedPreferences.getInstance();

// set value
await prefs.setInt('counter', counter);
```

## 3. Read data

## 3. 读取数据

To read data, use the appropriate getter method provided by the
`SharedPreferences` class. For each setter there is a corresponding getter.
For example, you can use the `getInt`, `getBool`, and `getString` methods.

要读取数据，请使用 `SharedPreferences` 类相应的 getter 方法。
对于每一个 setter 方法都有对应的 getter 方法。
例如，你可以使用 `getInt`、`getBool` 和 `getString` 方法。

<?code-excerpt "lib/partial_excerpts.dart (Step3)"?>
```dart
final prefs = await SharedPreferences.getInstance();

// Try reading data from the counter key. If it doesn't exist, return 0.
final counter = prefs.getInt('counter') ?? 0;
```

## 4. Remove data

## 4. 移除数据

To delete data, use the `remove()` method.

使用 `remove()` 方法删除数据。

<?code-excerpt "lib/partial_excerpts.dart (Step4)"?>
```dart
final prefs = await SharedPreferences.getInstance();

await prefs.remove('counter');
```

## Supported types

## 支持类型

Although key-value storage is easy and convenient to use,
it has limitations:

虽然使用 key-value 存储非常简单方便，
但是它也有以下局限性：

* Only primitive types can be used: `int`, `double`, `bool`, `string`,
  and `stringList`.
  
  只能用于基本数据类型： `int`、`double`、`bool`、`string` 和 `stringList`。
  
* It's not designed to store a lot of data.

  不适用于大量数据的存储。

For more information about shared preferences on Android,
see the [shared preferences documentation][]
on the Android developers website.

关于 Android 平台上 Shared Preferences 的更多信息，
请前往 Android 开发者网站上查看
[Shared preferences 文档][shared preferences documentation] 。

## Testing support

## 测试支持

It's a good idea to test code that persists data using
`shared_preferences`. You can do this by mocking out the
`MethodChannel` used by the `shared_preferences` library.

使用 `shared_preferences` 存储数据来测试代码是一个不错的思路。
为此，你需要模拟出 `shared_preferences` 库的 `MethodChannel` 方法。

Populate `SharedPreferences` with initial values in your tests
by running the following code in a `setupAll()` method in
your test files:

在你的测试中，你可以通过在测试文件的 `setupAll()` 方法中添加运行以下代码，
对 `SharedPreferences` 的值进行初始：

<?code-excerpt "lib/partial_excerpts.dart (Testing)"?>
```dart
TestDefaultBinaryMessengerBinding.instance.defaultBinaryMessenger
    .setMockMethodCallHandler(
        const MethodChannel('plugins.flutter.io/shared_preferences'),
        (methodCall) async {
  if (methodCall.method == 'getAll') {
    return <String, dynamic>{}; // set initial values here if desired
  }
  return null;
});
```

## Complete example

## 完整示例

<?code-excerpt "lib/main.dart"?>
```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of the application.
  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: 'Shared preferences demo',
      home: MyHomePage(title: 'Shared preferences demo'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key, required this.title});

  final String title;

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  @override
  void initState() {
    super.initState();
    _loadCounter();
  }

  //Loading counter value on start
  Future<void> _loadCounter() async {
    final prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0);
    });
  }

  //Incrementing counter after click
  Future<void> _incrementCounter() async {
    final prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0) + 1;
      prefs.setInt('counter', _counter);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: const Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}
```


[`shared_preferences`]: {{site.pub-pkg}}/shared_preferences
[shared preferences documentation]: {{site.android-dev}}/training/data-storage/shared-preferences
