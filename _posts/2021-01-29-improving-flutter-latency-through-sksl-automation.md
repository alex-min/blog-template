---
title: Improving Flutter latency through sksl automation
description: Faster apps with less time spent on manual tasks
author: alex-min
image: /images/screenshot-generation.png
tags:
  - flutter
---

# Improving Flutter latency through sksl automation

Here's a demo of the end result, automating the navigation through the whole app:

<video src="/videos/flutter-sksl.mp4" muted autoplay loop controls>
  <p>Video demo of the sksl generation where the app is playing itself</p>
</video>


## The core issue

Flutter performance is very critical when using production apps. There's a built-in way to improve performance & reduce jank called sksl shader caching.

Animations can appear laggy the first time you are going through the app. Once the shader cache kicks-in, it becomes faster again, we call this laggy effect *rendering jank*.
This is especially bad on iOS since iOS has a different rendering model which makes the app extra laggy on the shader generation.

Flutter has a built-in command to generate a cached shader file

```flutter run --profile --cache-sksl```

However, this is very tedious since it requires you to go through the whole app page by page manually and click on the most common screens where you think there's going to be some animation. The cached file is also built against a specific skia & Flutter version so every time you upgrade Flutter you have to regenerate it by hand. The Flutter team also advises to build two different cache file for iOS and Android, that doubles the amount of manual work on each release.

## Let's automate that boring job with Flutter driver!

Flutter driver is a way to automate taps & navigation through your app by using code. With a single command, the file can generate the shader cache file:

```flutter drive --profile --cache-sksl --write-sksl-on-exit android-shaders.sksl.json -t test_driver/sksl_generator.dart```

20 seconds later and Flutter going through every page of the app at light speed and the new shader cache is generated!
This is then used in the production build.

## Improving the reliability of the shader generation

Pages can take a variable amount of time to generate, buttons can be visible only after some animation has completed, slowdowns can happen...

The safest way to improve reliability of the shader generation is to wait for an element to be present first and then tapping on it.
This ensures that any animation has finished completion.  

Here is a small extension on the Flutter driver which does exactly that and an example code on how I tap on the custom currency input keyboard: 

```dart 
extension WaitForTap on FlutterDriver {
  waitForAndTap(SerializableFinder finder) async {
    await waitFor(finder);
    await tap(finder);
  }
}

// Moving through the calculator

Future<void> useCurrencyInput(FlutterDriver driver) async {
  await driver.waitForAndTap(find.byType('CurrencyInput'));

  for (var i = 0; i < 10; i++) {
    await driver.waitForAndTap(find.byValueKey('keyboard_delete'));
  }
  await driver.waitForAndTap(find.byValueKey('keyboard_2'));
  await driver.waitForAndTap(find.byValueKey('keyboard_1'));
  await driver.waitForAndTap(find.byValueKey('keyboard_hide'));
}
```

Now I can start this driver every time I want to deploy a production build, this driver also work for creating iOS shaders!




