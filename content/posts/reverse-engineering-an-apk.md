---
title: "Reverse Engineering an Apk"
date: 2021-05-19T12:46:15+03:00
draft: true
tags: ["reverse engineering", "android", "java"]
---

##### Trying out reverse engineering an Android application.
* * *

<!--more-->

> __Disclaimer!__ 
> This article does not in any way intend to promote piracy. Developers have their own warranted reasons for including adverts inside applications packages.

I found this particular Bible app on the Google Play Store and found it annoying (somewhat) that you had to get through an advert before using it.
Ads would pop up every time you read the verse of the day. So armed with nothing but good intentions I set out to try to get rid of the ads.

##### Tools

A list of the tools I used:

- [apktool](https://ibotpeaches.github.io/Apktool/) - disassemble Android application packages _(apks)_.
- [jadx](https://github.com/skylot/jadx) - decompile _Dex_/_Dalvik_ code to _Java_ code.
- [uber-apk-signer](https://github.com/patrickfav/uber-apk-signer) - sign, zip-align apks. Apks need to be signed if they are ever to be installed.
- [Android Device Emulator](https://developer.android.com/studio) (present within the Android Studio IDE)/Android phone.

I will not be explaining how to these tools to work, it's beyond the scope of this article/post. However, you need a certificate if you plan on signing an apk.
The only way (that I know of) to generate such a certificate would require a working installation of [Android Studio](https://developer.android.com/studio). This
[here](https://developer.android.com/studio/publish/app-signing#signing-manually) is a useful resource about signing apks.

##### The problem
