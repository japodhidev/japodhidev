---
title: "Reverse Engineering an Apk"
date: 2021-05-19T12:46:15+03:00
draft: true
tags: ["reverse engineering", "android", "java"]
---

##### Trying out reverse engineering an Android application.

<!--more-->

> __Disclaimer!__ 
> This article does not in any way intend to promote piracy. Developers have their own warranted reasons for including adverts inside applications packages.

I found this particular Bible app on the Google Play Store and found it annoying (somewhat), that you had to get through an advert before using it.
Ads would pop up every time you tried to use some of the app's features. So armed with nothing but good intentions I set out to try to get rid of the ads.

##### Tools

A list of the tools I used:

- [apktool](https://ibotpeaches.github.io/Apktool/) - disassemble Android application packages _(apks)_.
- [jadx](https://github.com/skylot/jadx) - decompile `Dex`/`Dalvik` code to `Java` code.
- [uber-apk-signer](https://github.com/patrickfav/uber-apk-signer) - sign, zip-align apks. Apks need to be signed if they are ever to be installed.
- [Android Device Emulator](https://developer.android.com/studio) (present within the Android Studio IDE)/Android phone.

I will not be explaining how these tools work, it's beyond the scope of this article/post. I should however mention that you need a certificate if you plan on signing an apk.
The only way (that I know of) to generate such a certificate would require a working installation of [Android Studio](https://developer.android.com/studio). This
[here](https://developer.android.com/studio/publish/app-signing#signing-manually) is a useful in-depth resource on how to go about signing apks.

##### The problem

[Interstitial Ads](). You may have heard of them or better still you may have encountered them. What they are is an unbearable annoyance that I desperately try to rid myself off of
 as this article would claim. There are varied opinions on the ethics of including them in applications. My opinion on the inclusion or lack thereof of said ads is not of any importance since
  I'm not an Android developer.

I found two instances of interstitial ads in this Bible application:

- The first one was a fullscreen ad that covered the entire screen on initial app launch.
- The second one (annoying one) would appear when you used a feature within the app. I'll be focusing on this particular one.

The screenshots below demonstrate what the app looked like with and without the ad:

![Screen1](/img/with-ad.png) ![Screen2](/img/without-ad.png)

##### Solution/s

Once the disassembly and decompilation was done, I was left with a directory structure that loosely resembles that of a typical Android Studio project. Of importance were:

- _res_ - containing all layout related `XML` files
- _sources_ - containing all decompiled `Java` classes, including any third party libraries.
- _AndroidManifest.xml_ - lists permissions and stuff. Useful if you want to change the name of the apk.

Most of the work lay in the _sources_ folder. I'll list a few methods I used to remove the ads now.

1. Premature return statements
    - This was the quick and dirty option. Simply placing a return statement before any ad related code is executed so that the function/method returns without performing any task.
    
    ```java
   private void requestNewInterstitial() {
      this.mInterstitialAd = new InterstitialAd(this);
      this.mInterstitialAd.setAdUnitId(getResources().getString(R.string.interstitial_ad_unit_id));
      this.mInterstitialAd.loadAd(new AdRequest.Builder().build());
    }
   ```

   ```java
   private void requestNewInterstitial() {
      return;
   }
   ```
2. Ensuring null checks fail
    - Another one of those easy fixes. Having a null check evaluate to true causes the code dependent on the null check not to be executed. Case in point, assigning a null value to the variable `this.mInterstitialAd`, ensures the if statement below will fail and as such any subsequent code will not be executed.
    
    ```java
    private void goToNextLevel() {
        this.mInterstitialAd = null;
        if (this.mInterstitialAd != null) {
            ...
    ```