# PGSkinPrettifyEngine
品果视频美肤引擎

## 支持的效果

* 磨皮美肤，强度可调
* 肤色的白晰、粉嫩、红润程度调节

实际处理效果可参看[刊例](http://www.camera360.com/filter/index.html)，或者下载Demo进行体验。

### Change Log
#### 2017.02.27
1. 修改了YUV色彩转换矩阵，尝试解决嘿秀反馈的播放端发黄问题。
2. 删除滤镜：Rokkor，Fuji，Prospect，Ultramax。
3. 增加滤镜：Memory，Mintgreen，Movie，Vista。
4. 初始化时输出当前版本号。

## 支持的平台
### iOS
* [Demo安装](http://www.camera360.com/filter/download.html?k=5xdNAApspj8DcVH2T9owooKC%2Bwe9h2u4jtDbHGbhWWsqaIU6nks3pw%3D%3D)
* [SDK引擎及Demo源码请联系我们](#联系我们) 
* [API文档](README_iOS.md)

### Android
* [Demo安装](http://www.camera360.com/filter/adrdownload.html?k=hylVnDMi62bhog%2BED1c819SPZ0zgYKpjT4sDY6IOLWs%3D)
* [SDK引擎及Demo源码请联系我们](#联系我们) 
* [API文档](README_Android.md)
* [SDK接入流程](Android-SDK接入.md)

## 性能测试
视频美肤引擎的处理性能测试为：从数据输入引擎开始，至引擎输出数据结束(不包括屏幕显示)。

分辨率	|iphone5 | iphone5s | iPhone6plus | iPhone6 | iPhone6s
:------- |:--------|:----- |:----- |:----- |:----- 
360p（480x360）| 4ms| 6ms| 5ms| 5ms| 4ms
480p（640x480）| 4ms| 7ms| 6ms| 6ms| 4ms
720p（1280x720）| 5ms| 7ms| 4ms| 5ms| 4ms

* 每帧处理耗时

分辨率	|	vivo x7 | 三星s7 | 三星note3  
:------- |:------|:----- |:----- 
480p（640x480）| 2ms| 4ms| 8ms
720p（1280x720）| 3ms| 3ms| 6ms
1080p（1920x1080）|3ms| 4ms| 4ms

* 增加了GetSkinPrettifyResult接口调用，获取美肤结果，接口性能

分辨率	|	vivo x7 | 三星s7 | 三星note3 
:------- |:------|:----- |:----- 
480p（640x480）| 6ms| 7ms| 10ms
720p（1280x720）| 11ms| 15ms| 25ms
1080p（1920x1080）| 85ms| 60ms| 50ms

* SDK内存消耗

分辨率	|	vivo x7 | 三星s7 | 三星note3 
:------- |:------|:----- |:----- 
480p（640x480）| 4M| 8M| 10M
720p（1280x720）| 4M| 8M| 10M
1080（1920x1080）| 7M| 14M| 15M

分辨率	|	iPhone5s | iPhone6 | iPhone6plus 
:------- |:------|:----- |:----- 
480p（640x480）| 11M| 11M| 9M
720p（1280x720）| 11M| 11M| 9M

* CPU占用

分辨率	|	vivo x7 | 三星s7 | 三星note3 
:------- |:------|:----- |:----- 
480p（640x480）| 3%| 6%| 5%
720p（1280x720）| 3%| 4%| 5%
1080p（1920x1080）| 3%| 10%| 5%

分辨率	|	iPhone5s | iPhone6 | iPhone6plus 
:------- |:------|:----- |:----- 
480p（640x480）| 28%| 25%| 31%
720p（1280x720）| 28%| 25%| 32%

## <b id='联系我们'>联系我们</b> 
### 声明
美颜SDK是一款付费产品，在产品里使用前需要联系商务，签署协议并获取对应的Key。

为了更好的为您服务，请填写一些[基本信息](http://www.camera360.com/filter/apply.html)。

### 商务合作及授权 Authorization

联系人 Contact	|职位 Title | Mobile | QQ | E_mail 
:------- |:--------|:----- |:----- |:----- 
杨颂 Amiel Yang | 商务合作总监 Director of Business Cooperation| 18615700177 13980706252| 2851258101| yangsong@camera360.com

### 技术支持 Technical 
Email: zhanglu@camera360.com 

QQ: 2851258101



