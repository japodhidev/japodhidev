---
title: "Reverse Engineering an Apk"
date: 2021-05-19T12:46:15+03:00
draft: false
tags: ["reverse engineering", "android", "java"]
---

##### Trying out reverse engineering on an Android application.

<!--more-->

> __Disclaimer!__ 
> This article does not in any way intend to promote piracy.
>
> Developers have their own warranted reasons for including adverts inside applications packages.

I found this particular Bible app on the Google Play Store and found it annoying (somewhat), that you had to get through an advert before using it.
Ads would pop up every time you tried to use some of the app's features. So armed with nothing but good intentions and curiosity I set out to try to get rid of the ads.

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
 as should be evident in this article. There are varied opinions on the ethics of including interstitial ads in applications. My opinion on the inclusion or lack thereof of said ads is not of any importance since
  I'm not an Android developer.

I found two instances of interstitial ads in this Bible application:

- The first one was a fullscreen ad that covered the entire screen on initial app launch.
- The second one (annoying one) would appear when you used a feature within the app. I'll be focusing on this particular one.

The screenshots below demonstrate what the app looked like with and without the ad:

{{< rawhtml >}}
    <div class="img-wrapper">
        <a href="#" data-bs-toggle="modal" data-bs-target="#staticBackdrop">
             <img src="/img/with-ad.png" alt="ad present" class="img-thumbnail">
        </a>
        <a href="#" data-bs-toggle="modal" data-bs-target="#staticBackdrop">
             <img src="/img/without-ad.png" alt="ad removed" class="img-thumbnail">
        </a>
    </div>
    
    <!-- Modal -->
    <div class="modal fade " id="staticBackdrop" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered modal-xl">
        <div class="modal-content bg-dark text-light">
          <div class="modal-header">
            <button type="button" class="btn-close bg-light" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body text-center">
            <p>Before(left) and after(right)</p>
            <div class="img-wrapper">
                <img src="/img/with-ad.png" alt="ad present">
                <img src="/img/without-ad.png" alt="ad present">
             </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>

{{< /rawhtml >}}

##### Solutions

Once the disassembly and decompilation was done, I was left with a directory structure that loosely resembles that of a typical Android Studio project. Of importance were:

- _res_ - directory containing all layout related `XML` files
- _sources_ - directory containing all decompiled `Java` classes, including any third party libraries.
- _AndroidManifest.xml_ - `XML` file that lists permissions and stuff. Useful if you want to change the name of the apk.

Most of the work lay in the _sources_ folder. I'll list a few methods I used to remove the ads now.

1. Premature/empty return statements
    - A quick and dirty method. Simply placing a return statement before any ad related code is executed so that the function/method returns without performing any task.
    
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
      // this.mInterstitialAd = new InterstitialAd(this);
      ...
   }
   ```
2. Ensuring null checks fail.
    - Another one of those easy fixes. Having a null check evaluate to true causes the code dependent on the null check not to be executed.
    Case in point, assigning a null value to the variable `this.mInterstitialAd`, ensures the if statement below will fail and as such any subsequent code will not be executed.
    
    ```java
    private void goToNextLevel() {
        this.mInterstitialAd = null;
        if (this.mInterstitialAd != null) {
            this.mInterstitialAd.setAdListener(new AdListener() {
                ...
    ```
3. Reversing comparison operators.

    - A thoughtful `<` in place of a `>` or vice-versa would suffice.
    The code below compares the value of a random integer between 0-100 against the value of the integer represented by the variable `R.string.app_code_number (2131820585)`. The comparison would always evaluate to true.
    By replacing the `<` _less than_ operator with the `>` _greater than_ operator, the check would fail, and the subsequent code would not be executed.
    
    ```java
    Random random = new Random();
    if (random.nextInt(100) < Integer.valueOf(AdMainActivity.this.getResources().getString(R.string.app_code_number)).intValue()) {
        if (AdMainActivity.this.v.isAdLoaded()) {
        AdMainActivity.this.v.displayLoadedAd();
        AdMainActivity.this.v.mInterstitialAd.setAdListener(new AdListener() {
        ...
    ```
   
   ```java
       Random random = new Random();
       if (random.nextInt(100) > Integer.valueOf(AdMainActivity.this.getResources().getString(R.string.app_code_number)).intValue()) {
           // No execution. 
           //if (AdMainActivity.this.v.isAdLoaded()) {
           //AdMainActivity.this.v.displayLoadedAd();
           //AdMainActivity.this.v.mInterstitialAd.setAdListener(new AdListener() {
           ...
   ```
4. Unloading/removing all code, layouts and views related to ads.
    - This was the OP method. It was also the hardest and the most time-consuming. That makes it the best method.
    Within the _res_ folder lay an `XML` file that held the layout for the ad:
    
    ```xml
    <LinearLayout n1:gravity="center" n1:id="@id/layout_adView" n1:visibility="gone" n1:layout_width="fill_parent" n1:layout_height="wrap_content" n1:layout_marginTop="@dimen/ads_padding" n1:layout_marginBottom="@dimen/ads_padding">
        <com.google.android.gms.ads.AdView n1:id="@id/adView" n1:layout_width="wrap_content" n1:layout_height="wrap_content" n3:adSize="MEDIUM_RECTANGLE" n3:adUnitId="@string/banner_ad_unit_id" />
    </LinearLayout>
    ```
   Removing it would surely break that application, so I followed its usages to a `Java` class. I managed to find this reference to the layout above:
   
   ```java
   this.r = (LinearLayout) findViewById(R.id.layout_adView);
   ...
   ```
   For this method to work I had to find every such occurrence and either cleverly comment it out or remove it entirely.
   This proved to be even more difficult since I had to also search for and remove any additional code that depended on it. 
   I soon realized that this method would require a lot of time and decided to go with the other options.
   
   I had finally achieved what I set out to do. The Bible app was ad free and working as I wanted it to. 
   I've been using it for a few days, and I haven't encountered any unwanted ads.
   
##### Conclusion

There may be better ways to get rid of unwanted ads. There may also be better ways to do what I just did.
I found reverse engineering (if I can call this that) both challenging and exciting.

It wasn't my intention to undermine the app developer's work in any way, so I can surely be forgiven for being inquisitive.