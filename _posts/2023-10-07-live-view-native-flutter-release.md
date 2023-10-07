---
title: Releasing LiveView native with Flutter preview
description: A LiveView native client built in Flutter!
author: alex-min
image: /images/flutter-live-view-cover.png
tags:
  - flutter
  - elixir
  - phoenix
---

I've mentioned in my previous post working on a Live View Flutter Client, here is now the open source release!

## What is LiveView native?

LiveView native is a new way to build mobile apps with Phoenix LiveView in Elixir.
There's two native clients being built at the moment, one with Swift for the Apple platform (which is basically almost complete) and one in Kotlin mainly targetting Android (which is currently being built).

I wanted a client using Flutter and since nobody made any, I'm building it!

## What does it look like?

Here is a sneak peek on my computer of a LiveView with three devices, an Android target, a Linux target and a web target:

<video src="/videos/demo-flutter-live-view.mp4" muted autoplay loop controls>
  <p>Video demo of the live view flutter client</p>
</video>

Everything communicates together an all three targets are sharing the same counter !

### Where is the code?

The source code [is here](https://github.com/alex-min/live_view_native_flutter_client), happy hacking!

### What's missing?

Pretty much everything is missing at the moment, I need more styling, forms, live interop, hook, themes... The road ahead is pretty long.
I have a lot of experience with flutter since I built a full mobile app with it (~30k lines of code) so I know where I'm going. 
g
This is the priorities I'm working on right now:
 - Implement all LiveView events
 - All the Flutter components (only a subset are implemented in this demo)
 - Routing
 - Making the full Flutter styling API accessible
 - LiveView hooks with an embed js engine
 - Themes
 - ...

Stay tuned!