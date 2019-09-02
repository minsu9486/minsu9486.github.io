---
title: "LINE: JoJo's Bizarre Adventure SS"
permalink: /portfolio/jojo/
date: 2015-11-01
excerpt: "A 2D top-down shooter in IOS/Android where combat against enemies is done using Medal."
header:
  image: /assets/images/jojo_main_wide.png
  caption: "[YouTube Link](https://www.youtube.com/watch?v=ktEDtEFHqGQ)"
  teaser: /assets/images/jojo_icon.png
professional: true
company: "[Funigloo](http://www.funigloo.com/)"
engine: "Cocos2d-x"
platform: "Android & iOS mobile"
skills: "C++, Java"
role: "Client side engineer"
responsibilities: "Android & localization with LINE Corp."
toc: true
toc_label: "Table of Contents"
toc_sticky: true
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

### Game Description
<figure class="third">
	<img src="/assets/images/jojo_desc_1.jpeg">
	<img src="/assets/images/jojo_desc_2.jpeg">
	<img src="/assets/images/jojo_desc_3.jpeg">
	<figcaption>This is a 2D top-down shooter in Android/iOS, where combat against enemies is done using Medal, particularly "Striker" Medals, each of which represents one character and their ability set.</figcaption>
</figure>


### Assigned Tasks
 - Implemented a compatible process between Java and C++ using [JNI](https://docs.oracle.com/javase/7/docs/technotes/guides/jni/)
 - 3 languages (English, Thai, Taiwanese) supporting system with UTF-16 issue
 - Adding [LINE SDK](https://developers.line.biz/en/) with related social contents and UI
 - Maintaing the live service

### Code
 - A compatible process between C++ and Java
   - [LineTranslator.h](/scripts/jj-line-translator.h/)
   - [LineTranslator.cpp](/scripts/jj-line-translator.cpp/)
 - A language manager for English, Thai and Taiwanese (UTF-16)
   - [LanguageManager.h](/scripts/jj-language-manager.h/)
   - [LanguageManager.cpp](/scripts/jj-language-manager.cpp/)
 - A part of code which cope with emoji characters (UTF-8)
   - [UtilFunc.h](/scripts/jj-util-func.h/)
   - [UtilFunc.cpp](/scripts/jj-util-func.cpp/)

### [App Info Archive](https://apkpure.com/line-jojo%E2%80%99sbizarreadventuress/com.linecorp.LGJOTW)
{: style="text-align: center;"}
