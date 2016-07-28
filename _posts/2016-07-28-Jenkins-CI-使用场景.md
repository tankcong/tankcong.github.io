---
layout: post
title: Android应用开发持续集成
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
    - Java
---


## Jenkins在Android应用开发的使用

### Why Jenkins?
  
&emsp;&emsp;在平常Android应用开发项目中，PM跟客户需要及时的了解项目进展，QA在中后期也需要多轮次的
测试和回归，这往往伴随着频繁的打包集成需求。对开发者来说，尤其是项目的主程，就不得不经常性的中
断手上的开发工作来打包或者发布新版。That's so bad...  
&emsp;&emsp;[Jenkins](jenkins-ci.org) really saved my day.  
&emsp;&emsp;简单地说，Jenkins可以持续**自动**的构建、打包、发布软件项目。典型的使用是Jenkins自动
拉取最新代码，自动完成APK打包并发布到第三方平台，发布后用邮件通知相关人员进行下载或测试。


### Jenkins的几种使用场景

&emsp;&emsp;对于移动开发的团队，因为团队规模、项目特点的不同，可以有几种灵活的Jenkins使用方式：  

1. 对于规模极小的团队，无服务器资源甚至缺少QA。Jenkins可以部署在开发者的电脑上，开发者正常
自测完毕后提交代码推送tag触发自动打包，完成后自动上传蒲公英等平台并邮件通知各位去蒲公英平台
下载。  
   这种使用方式开发者只需要提交代码就ok，节省了开发者clean/build/upload/mailing的过程。
但缺点是编译的过程仍旧在开发者的电脑上，编译时电脑卡卡的。  

2. 如果公司允许QA接触代码，那么可以在QA的电脑部署Jenkins，配置代码接入及编译环境，开发者
推送代码后即可继续接下来的开发，由QA在自己电脑的Jenkins上完成打包、上传、通知等动作，QA还可
以利用Jenkins Plugin配置直接进行自动化测试。  
   这种方式已经解放了开发者，但是对QA的有一定的要求，需要QA了解自动化测试、编译环境配置等技术。  

3. 如果公司内有服务器资源，可以直接在服务器上配置Jenkins及编译环境。开发者推送代码后由服
务器自动完成打包、代码检测、内测平台上传、邮件通知等一系列操作。开发者、QA、PM、客户们各取
所需。典型的应用：    
   
   * 开发者在dev分支完成代码合并、自测，推送tag "build"
   * Server Jenkins接收到通知，sync codes/clean/build/static code analysis/uploading/mailing..
   * 开发者检查Jenkins [Android Lint Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Android+Lint+Plugin)
   等插件静态代码分析报告
   * PM/客户收到邮件内第三方平台应用地址去下载体验
   * QA更新应用立即开始测试，并且根据commit change log针对性验证等


### 成果  

  build结果页：
  ![](https://github.com/tankcong/tankcong.github.io/blob/master/img/in-post/jenkins_build_status.jpeg)  

  lint issues:
  ![](https://github.com/tankcong/tankcong.github.io/blob/master/img/in-post/jenkins_lint_issues.jpeg)  

  line report:
  ![](https://github.com/tankcong/tankcong.github.io/blob/master/img/in-post/lint_report.jpeg)


### Further Reading

* [Jenkins持续集成Android项目](https://segmentfault.com/a/1190000004628020)
* [使用Jenkins搭建iOS/Android持续集成打包平台](http://debugtalk.com/post/iOS-Android-Packing-with-Jenkins/)
* [实现Android CI](http://www.infoq.com/cn/articles/realize-android-ci)
* [Static analysis build server for Android with Jenkins using Docker](https://medium.com/@Rapchik/static-analysis-build-server-for-android-with-jenkins-using-docker-bda888d4b34e#.xxe5ppqqc)








