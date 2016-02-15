---
layout: post
title: Android Systrace, problems finishing
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
    - Optimization
---

### Procedure

1. setup developer options in settings, [ref](http://www.curious-creature.com/2012/12/01/android-performance-case-study/)  
2. start app with opengl trace  
  `adb shell`  
  `am start --opengl-trace com.app.package/com.app.package.Activity`  
3. run systrace.py in `sdk/platform-tools/systrace`, eg:  
  `./systrace.py -a com.tencent.nbagametime gfx view wm --time=10`
4. run method profiling tool in Android Studio Monitor or DDMS
5. analyze `trace.html` and method profiling file `*.trace` together

### Resources

* [Android Performance Case Study](http://www.curious-creature.com/2012/12/01/android-performance-case-study/)  
* [Speed up your app](http://blog.udinic.com/2015/09/15/speed-up-your-app)  
* [Systrace](http://developer.android.com/intl/es/tools/help/systrace.html)  
* [Analyzing UI Performance with Systrace](http://developer.android.com/intl/es/tools/debugging/systrace.html)  
* [Profiling with Traceview and dmtracedump](http://developer.android.com/intl/es/tools/debugging/debugging-tracing.html)  
* [Android Graphics Performance](https://speakerdeck.com/romainguy/android-graphics-performance)

### Current Progress

[trace.html](http://www.tankcong.com/attach/trace.html)
<img src="http://www.tankcong.com/img/in-post/trace.png"/>  

Got so much work to do..  
I'll write anothers blog when I finish doing these alerts.