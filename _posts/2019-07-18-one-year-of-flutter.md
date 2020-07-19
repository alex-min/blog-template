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

Flutter is built with Dart, a Javascript/Java inspired language. Dart was built internally at Google with the specific goal to replace Javascript on the web as a saner language, with better defaults. Unfortunately for the Dart team, the project never took off, Dart was stuck in a limbo state for a few years while still being maintained for internal Google needs.

The language itself had very good features which enabled other uses cases:
 - The Dart VM is a wonderful piece of technology, build with hot reloading at its core
 - The language can be interpreted or compiled natively for better performance
 - The Dart tooling around the language well built

I fully expected the language to be an issue on the project since it's so niche, but actually it has been a great experience on that level. It's quick to learn and very easy to pick up, especially if you have done some Javascript previously. 

## The good parts

### Tooling

The Flutter tooling is one of it's strong parts. Hot reloading your code **actually** works, Flutter is almost the only piece of technology I've ever used where the hot reload was working as well as it is. Generally I would disable it elsewhere, it even makes you miss it in other languages.

The package manager (pub) works great, upgrades are smooth (if you are coming from the npm world, it's honestly a breath of fresh air). After a year of managing my project, I only had a single conflict where I had to pin a specific version of a package to avoid a compilation error with the main Flutter SDK, that only lasted two weeks and then upgrades were smooth again.

The Flutter SDK has multiple channels (master, dev, beta and stable), the stable version is quite good as not reintroducing regressions and flutter upgrades can be done from the command line easily. 

The language server in VSCode works perfectly, autocompletion also works wonders, the Dart code formatter is also one of the best I used, I consider it on par with Go, which is one of the best in my opinion.


### Performance & Animations

Flutter uses Skia, an internal and very performant render engine which draws everything to a canvas. Performance is definitely a strong point of the platform. I use a very cheap Samsung J3 as my daily driver which is almost the bottom of the barrel in terms of performance in 2020. Basically every app on my phone is lagging noticeably, including Google apps. My budget app on the other hand is the quickest app on my phone, by a long margin. If you are targetting a market with a lot of low-end budget phones, Flutter is exactly what you need.

Animations are also a very strong point of this platform, high-speed animations can be built with [Rive](https://rive.app/) and imported directly into Flutter, rendered at full speed and weighting a few Kb. I do have a few toy animations built with Rive on my app.

### Portability

I have not published the iOS version yet due to Apple developer account issues but my budget app already works without much effort identically on Android, Linux, Windows & macOS thanks to [Hover](https://github.com/go-flutter-desktop/hover).

If you are building a desktop app, Flutter can be a good technology to keep in mind. It produces small binaries, fast and reactive applications, works cross-platform and won't use too much RAM like most Electron apps.

Desktop support is still in an alpha quality at the moment so expect some missing features, you might have to dig into the code and make some pull requests.

### The Flutter SDK

The SDK is quite complete, a lot of basic and complex components are there. The SDK has material design guidelines by default, which might be a good thing or a bad thing depending on what you are trying to achieve.

While you can make your application look native with more work but generally it's not what you would use Flutter for. Flutter shines primarily as building complex apps which have their own design goals.


### Styling

Styling will look familiar if you are coming from a web background. You will have a stripped-down version of flex, padding & margins, border-radius, font-weight...

The main difference being that there's a global Flutter theme which applies to your whole app instead of classes. This makes it easier to swap the theme to make a dark theme.

I'm still trying to make my stying code looks nicer, extending the main theme seems to be the right approach.


## The parts which would need improvements

### Testing

Testing isn't that bad, especially if you are coming from the Javascript world. A lot of the testing building blocks you expect are there and you do have a driver where you can tap on elements and see what happens.


<figure class="screenshot" markdown="1">

![A screenshot of a terminal showing that 174 tests passed in 4 minutes 43 seconds](/images/flutter-test-terminal.png)
<figcaption>Yay! Hopefully everything works.</figcaption>
</figure>

If you come from the Ruby world however, the testing will feel pretty average and you might find yourself needing a bit more boilerplate than you would expect. There's definitely some room to make testing more enjoyable and faster to write.

There is also no mutation testing in Dart yet (that I'm aware of), so you will have to use the code coverage tool extensively in the mean time and try to catch as much as you can. I had a pretty blocking bug on the code coverage tool which has been fixed in one of the more recent stable versions so I can recommend it now.

### Navigation

Navigation works stateless in Flutter. That can be a great thing, especially when you are importing packages, no need to plug your router for using packages, there's one big issue however, it's not really possible to micro-manage the view state when you use the go back button on Android.

That can be a problem for knowing which route you are on at the moment if you want to update the bottom bar with an indicator for example. As far as I know, it's still an unsolved problem, unless you rebuild an entire navigation system yourself with Provider or another Flutter state management library. I'll probably do that in the future but that's a lower priority bug at the moment.

### Webviews

If you need Webviews for your project (which I needed), it might be a blocker in the current state. Webviews don't support multiple tabs (which means *window.open* won't work in Javascript), the view might crash and there is poor keyboard support at the moment.

Additionally, this alpha-state Webview is only available on Android and iOS, not on any desktop platform yet.

## Conclusion

As far as the Flutter platform goes, it's been a pretty enjoyable experience for me. I will write one or more retrospective blog post on this project. I do recommend Flutter and even as a web developer, where you might prefer React Native initially due to the familiarity with Javascript, I would recommend trying Flutter, it will surprise you. 


