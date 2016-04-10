---
layout: post
title: Android Versioning, Android应用版本号规范
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
    - Dev
---

## Android Versioning

平常的开发中一直忽略了版本号管理的策略，目前的使用几乎毫无规则。  
这里主要参考[Semantic Versioning(语义化版本号)](http://semver.org/)整理一下目前的版本号发布
策略。

### Convention

版本号的形式为**MAJOR.MINOR.REVISION.BUILD**:  

* MAJOR, 主版本号，重大版本重写并且程序无法保证API兼容性时更新
* MINOR, 此版本号，软件新功能引入并且保持API兼容时更新
* REVISION, 修订(维护)版本，向后兼容的bug fixes时更新
* BUILD, build number，每次编译或者代码提交时更新

### SPEC

* 每次版本发布后，*MUST NOT*改变软件内容，任何修改*MUST*以新版本release
* 各自区段分别递增，除了BUILD段始终自增外其余XYZ段每当高位递增，低位依次归零。(1.2.9.107 -> 1.3.0.108)
* 初次开发时版本为0.y.z.b，正式发布后版本为1.0.0.BUILD NUMBER。对于Android应用，
BUILD NUMBER以VERSION CODE的形式存在，也可以体现在版本名中
* 如果有PRE-RELEASE，格式为X.Y.Z.B.P。P的格式为`[alpha|beta|rc]+[0-9A-Za-z-]`。优先
级alpha < beta < rc。(1.0.0.12-alpha+001, 1.0.0.13-beta+exp.sha.5114f85)
* 版本号的格式为X.Y.Z.B，XYZB全部为*数字*格式，优先级X > Y > Z > B

### Reference

* [Semantic Versioning 2.0.0](http://semver.org/)
* [android-auto-version](http://www.race604.com/android-auto-version/)
* [What “version naming convention” do you use?](http://programmers.stackexchange.com/questions/3199/what-version-naming-convention-do-you-use)

