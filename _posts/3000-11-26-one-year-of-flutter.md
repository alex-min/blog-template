---
title: One year of Flutter as a web developer
description: Looking back on a side project
author: alex-min
image: /images/logo_flutter.png
tags:
  - flutter
  - tech
---

I spent a considerable time on my [budget app](https://mavio.fr) side project built in Flutter. After a year of development and 25k lines of code later, I wanted to create a blog post about how everything went and my feedback on working with this framework.

## The Dart language

Flutter is built with Dart, a Javascript/Java inspired language. Dart was built internally at Google with the specific goal to replace Javascript on the web as a saner language, with better defaults. Unfortunatly for the Dart team, the project never took off, Dart was stuck in a limbo state for a few years while being maintained for internal Google needs.

The language itself had very good features which enabled other uses cases:
 - The Dart VM is a wonderful piece of technology, build with hot reloading at its core
 - The language can be interpreted or compiled natively for better performance
 - The Dart tooling around the language well built

I fully expected the language to be an issue on the project since it's so niche but actually it definitly has been a great experience on that level. It's quick to learn and very easy to pick up, especially if you have done some Javascript previously. 

## The good parts

### Tooling

The Flutter tooling is one of it's stongest part. Hot reloading your code **actually** works, Flutter is almost the only piece of technology I've ever used where the hot reload was working as well as it is. Generally I would disable it elsewhere, it even makes you miss it in other languages.

The package manager (pub) works great, upgrades are smoothless (if you are coming from the npm world, it's honestly a breath of fresh air). After a year of managing my project, I only had a single conflict where I had to pin a specific version of a package to avoid a compilation error with the main flutter sdk, that only lasted two weeks and then upgrades were smooth again.

The flutter sdk has multiple channels (master, dev, beta and stable), the stable version is quite good as not reintroducing regressions and flutter upgrades can be done from the command line easily. 

The language server in VSCode works perfectly, autocompletion also works wonders, the Dart code formatter is also one of the best I used, I consider it on par with Go, which is one of the best in my opinion.


### Performance

Flutter uses Skia, an internal and very performant render engine which draws everything to a canvas. Performance is definitely a strong point of the platform. I use a very cheap Samsung J3 as my daily driver which is almost the bottom of the barrel in terms of performance in 2020. Basically every app on my phone is lagging noticeably, including Google apps. My budget app on the other hand is the quickest app on my phone, by a long margin. If you are targetting a market with a lot of low-end budget phones, Flutter is exactly what you need.

Animation is also a very strong point of this platform, high-speed animations can be built with [Rive](https://rive.app/) and imported directly into Flutter, rendered at full speed and weighting a few Kb.

### Portability

I have published the iOS version yet due to Apple developer account issues but my budget app already works without much effort identically on Android, Linux, Windows & MacOS.

If you are building a desktop app, Flutter can be a good technology to keep in mind. It produces small binaries, fast and reactive applications, works cross-platform and won't use too much RAM like most Electron apps.

### The Flutter Sdk

The SDK is quite complete, a lot of basic and complex components are there. The SDK has material design guidelines by default 

## The parts which would need improvements

### Testing

Testing isn't that bad, especially if you are coming from the Javascript world. A lot of the testing building blocks you expect are there and 

### Navigation

Navigation works stateless in Flutter. That can be a great thing, especially when you are importing packages, no need to plug your router for using packages, there's one big issue however, it's not really possible to micro-manage the view state when you use the go back button on Android.

That can be a problem for knowing which route you are on at the moment if you want to update the bottom bar with an indicator for example. As far as I know, it's still an unsolved problem, unless you rebuild an entire navigation system yourself.

### Webviews

If you need Webviews for your project (which I did myself), it might be a blocker in the current state. Webviews don't support multiple tabs (which means any window.open won't work), might crash and have poor keyboard support.

Additionally, this alpha-state webview is only available on Android and iOS, not on any desktop yet.



