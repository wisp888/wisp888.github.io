---
title: Ubuntu wine 修改字体
date: 2019-11-08 16:05:39
tags: 
  - ubuntu
  - wine
categories: 
  - ubuntu
---

今天在Ubuntu安装WPS的时候，修复WPS的字体报错，从Windows复制了几个字体过来重新生成了一下缓存，突然WineQQ和微信的字体也变了，摸索了一下修改一发Wine字体：

<!--more-->

每个wine程序都有自己的一套字体目录：
```
/home/wisp/.deepinwine/Deepin-QQLight/drive_c/windows/Fonts/
/home/wisp/.deepinwine/Deepin-WeChat/drive_c/windows/Fonts/
```

下载simsun.ttc 字体，放到各个程序的Fonts目录下面去，然后创建注册表：
zh.reg
```
REGEDIT4
[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes]
"Arial"="simsun"
"Arial CE,238"="simsun"
"Arial CYR,204"="simsun"
"Arial Greek,161"="simsun"
"Arial TUR,162"="simsun"
"Courier New"="simsun"
"Courier New CE,238"="simsun"
"Courier New CYR,204"="simsun"
"Courier New Greek,161"="simsun"
"Courier New TUR,162"="simsun"
"FixedSys"="simsun"
"Helv"="simsun"
"Helvetica"="simsun"
"MS Sans Serif"="simsun"
"MS Shell Dlg"="simsun"
"MS Shell Dlg 2"="simsun"
"System"="simsun"
"Tahoma"="simsun"
"Times"="simsun"
"Times New Roman CE,238"="simsun"
"Times New Roman CYR,204"="simsun"
"Times New Roman Greek,161"="simsun"
"Times New Roman TUR,162"="simsun"
"Tms Rmn"="simsun"
```

导入zh.reg
```
WINEPREFIX="/home/wisp/.deepinwine/Deepin-QQLight/" deepin-wine  regedit zh.reg
WINEPREFIX="/home/wisp/.deepinwine/Deepin-WeChat/" deepin-wine  regedit zh.reg
```

打开QQ，喵喵效果
搞完收工~