---
layout: post
title: Android Application Time Synchronization
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
---

## Android Application Time Synchronization

  很多应用的客户端业务都要求C/S时间同步、准确（游戏、IM，秒杀等），由于系统时间是可以被用户随时修改的，所以需要在客户端内同步Server时间。
解决的方案就是在应用进入时同步自己的服务器时间，客户端始终维护这个同步到的服务端时间。如果应用进程结束，则重新同步时间。  
  同步时间可以参考NTP的思想利用简单的Api实现时间同步和延迟校正，那么如何确保在任何情况下该时间不会被用户的操作所影响？

### [android.os.SystemClock#elapsedRealTime](https://developer.android.com/reference/android/os/SystemClock.html#elapsedRealtime())

  下面是`SystemClock`的文档：

>
`elapsedRealtime()` and `elapsedRealtimeNanos()` return the time since the system was booted, and include deep sleep. This clock is guaranteed to be monotonic, and continues to tick even when the CPU is in power saving modes, so is the recommend basis for general purpose interval timing.
>

  **PROBLEM SOLVED**  

  简单的解决方案[ServerDate.java](https://gist.github.com/tankcong/a9120956f23ea924c81c64b3f35843f4):  
<script src="https://gist.github.com/tankcong/a9120956f23ea924c81c64b3f35843f4.js"></script>