# SDK简介

设备网络SDK是基于设备私有网络通信协议开发的，为嵌入式网络硬盘录像机、NVR、视频服务器、网 络摄像机、网络球机、解码器、报警主机等网络产品服务的配套模块，用于远程访问和控制设备软件的二 次开发。  

本文档仅介绍DVR、NVR支持的功能及相关接口，相关结构体和更多其他功能接口请参考《设备网络 SDK 使用手册.chm》。  

## 1. 适用产品型号

适用于但不仅限于以下产品型号：   

- DVR：DS-9100、DS-8100、DS-8000-S、DS-8800、DS-7800、DS-7300、DS-7200、DS-7100、DS-7000 等系 列，包括-ST、-SH、-SE、-SN、-RT、-RH、-XT等。  
- NVR：DS-96000N-I24/I16、DS-96000N-F24/F16(/H)(/I)、DS-96000N-H24/H16(/H)(/I)、DS-9600N-I8/H8/F8/ST/XT、 DS-8600N-I8/H8/F8/E8/ST/XT、DS-7800N-E1/SN/SNH、DS-7600N-ST/E2/E1、DS-7700N-ST/E4、DS-9500N-ST、 DS-9500N-S、DS-9600N-SH、DS-7600N-S、DS-9664N-RX 等。  
- XVR：DS-9000HQH-SH、DS-8100HQH(/HGH)-SH、DS-8000HQH-SH、DS-7300HQH(/HGH)-SH、 DS-7200HQH(/HGH)-SH、DS-8800HQH(/HGH)(/HUH)-SH(/Fx)、DS-7900HQH(/HGH)(/HUH)-SH(/Fx)、 DS-7800HQH(/HGH)(/HUH)-SH(/Ex/Fx)、DS-7100HGH-E1(/F1)等。  
- HDVR(混合型DVR)：DS-9000、DS-8000-ST、DS-7600H-ST/-S 系列等。  

## 2. SDK主要功能

设备网络SDK主要功能 ：

主要用于实时码流预览、录像文件回放和下载、云台控制、布防/撤防、语音对讲、日志管理、远程升 级、格式化硬盘、参数配置（系统配置、通道配置、串口配置、报警配置、用户配置）和获取设备能力集 等。    
本版本的设备网络SDK开发包中包含以上各个组件，HCNetSDK.dll、HCCore.dll必须加载（对于Linux SDK， 即libhcnetsdk.so、libHCCore.so），其他组件，用户可以根据需要选择其中的一部分或者全部，以下将对各个 组件在SDK中的作用和使用条件分别说明。 

设备网络SDK包含网络通讯库、播放库等功能组件，我们提供Windows和Linux两个版本的SDK，各 自所包含的组件如下：  

### 2.1 Windows SDK 组件

- 表 1.1 Windows SDK 组件

  | 库类型     | 组件类型             | 文件名称                  | 文件说明   | 备注               |
  | ---------- | -------------------- | ------------------------- | ---------- | ------------------ |
  | 网络通讯库 | 外部接口             | HCNetSDK.h                | 头文件     |                    |
  | 〃         | 〃                   | HCNetSDK.lib              | LIB 库文件 |                    |
  | 〃         | 〃                   | HCNetSDK.dll              | DLL 库文件 |                    |
  | 〃         | 核心组件             | HCCore.lib                | LIB 库文件 |                    |
  | 〃         | 〃                   | HCCore.dll                | DLL 库文件 |                    |
  | 组件库     | 设备配置核心组件     | HCCoreDevCfg.dll          | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 预览组件             | HCPreview.lib             | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 〃                   | HCPreview.dll             | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 回放组件             | HCPlayBack.dll            | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 语音组件             | HCVoiceTalk.dll           | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 报警组件             | HCAlarm.lib               | LIB 库文件 | HCNetSDKCom 文件夹 |
  |            | 〃                   | HCAlarm.dll               | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 显示组件             | HCDisplay.dll             | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 行业应用管理配置组件 | HCIndustry.dll            | DLL 库文件 | HCNetSDKCom 文件夹 |
  |            | 维护管理配置组件     | HCGeneralCfgMgr.lib       | LIB 库文件 | HCNetSDKCom 文件夹 |
  |            | 〃                   | HCGeneralCfgMgr.dll       | DLL 库文件 | HCNetSDKCom 文件夹 |
  | RTSP通讯库 |                      | StreamTransClient.dll     | DLL 库文件 | HCNetSDKCom 文件夹 |
  | 转封装库   |                      | SystemTransform.dll       | DLL 库文件 | HCNetSDKCom 文件夹 |
  | 字符转码库 |                      | libiconv2.dll             | DLL 库文件 | HCNetSDKCom 文件夹 |
  | 模拟能力集 |                      | LocalXml.zip              | XML文件包  |                    |
  | 帧分析库   |                      | AnalyzeData.dll           | DLL库文件  | HCNetSDKCom文件夹  |
  | 语音对讲库 |                      | AudioIntercom.dll         | DLL库文件  | HCNetSDKCom文件夹  |
  | 〃         |                      | OpenAL32.dll              | DLL库文件  | HCNetSDKCom文件夹  |
  | 播放库     | 核心库文件           | PlayM4.h、WindowsPlayM4.h | 头文件     |                    |
  | 〃         | 〃                   | PlayCtrl.lib              | LIB库文件  |                    |
  | 〃         | 〃                   | PlayCtrl.dll              | DLL库文件  |                    |
  | 〃         | 视频渲染库           | SuperRender.dll           | DLL库文件  |                    |
  | 〃         | 音频渲染库           | AudioRender.dll           | DLL库文件  |                    |
  | 〃         | 小鹰眼库             | EagleEyeRender.dll        | DLL库文件  |                    |
  | 〃         | GPU硬解码库          | HWDecode.dll              | DLL库文件  |                    |
  | 〃         | 鱼眼库               | MP_Render.dll             | DLL库文件  |                    |
  | 〃         | 视频后处理库         | MP_VIE.dll                | DLL库文件  |                    |
  | 〃         | 测温信息抓图库       | YUVProcess.dll            | DLL库文件  |                    |
  | 〃         | DirectX组件库        | D3DCompiler_43.dll        | DLL库文件  |                    |

### 2.2 Linux SDK 组件

- 表 1.2 Linux SDK 组件

  | 库类型      | 组件类型             | 文件名称                | 文件说明     | 备注               |
  | ----------- | -------------------- | ----------------------- | ------------ | ------------------ |
  | 网络通讯库  | 外部接口             | HCNetSDK.hHCNetSDK.h    | 头文件头文件 |                    |
  | 〃          | 〃                   | libhcnetsdk.so          | SO库文件     |                    |
  | 〃          | 核心组件             | libHCCore.so            | SO库文件     |                    |
  | 组件库      | 设备配置核心组件     | libHCCoreDevCfg.so      | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 预览组件             | libHCPreview.so         | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 回放组件             | libHCPlayBack.so        | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 语音组件             | libHCVoiceTalk.so       | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 报警组件             | libHCAlarm.so           | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 显示组件             | libHCDisplay.so         | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 行业应用管理配置组件 | libHCIndustry.so        | SO库文件     | HCNetSDKCom文件夹  |
  | 〃          | 维护管理配置组件     | libHCGeneralCfgMgr.so   | SO库文件     | HCNetSDKCom文件夹  |
  | hpr 库      |                      | libhpr.so               | SO库文件     |                    |
  | RTSP 通讯库 |                      | libStreamTransClient.so | SO 库文件    | HCNetSDKCom 文件夹 |
  | 转封装库    |                      | libSystemTransform.so   | SO 库文件    | HCNetSDKCom 文件夹 |
  | 字符转码库  |                      | libiconv2.so            | SO 库文件    | HCNetSDKCom 文件夹 |
  | 帧分析库    |                      | libanalyzedata.so       | SO 库文件    | HCNetSDKCom 文件夹 |
  | 播放库      | 核心库文件           | PlayM4.h、LinuxPlayM4.h | 头文件       |                    |
  | 〃          | 〃                   | libPlayCtrl.so          | SO 库文件    |                    |
  | 〃          | 视频渲染库           | libSuperRender.so       | SO 库文件    |                    |
  | 〃          | 音频渲染库           | libAudioRender.so       | SO 库文件    |                    |

### 2.3 库说明

#### 2.3.1 网络通讯库

设备网络SDK的主体，主要用于网络客户端与各类产品之间的通讯交互，负责远程功能调控, 远程参数配置及码流数据的获取和处理等。设备网络 SDK V5.0针对产品应用业务进行细化，对之前版本的SDK的功能模块进行组件化，其中外部接口（HCNetSDK.dll）仍然保持和设备网络SDK V4.x版本保存一致(向下兼容)，其他单独的业务功能（预览、回放等）可以加载单独的模块组件，多个业务功能也可以组合使用。更新SDK时，HCNetSDK.dll、HCCore.dll以及HCNetSDKCom文件夹下的功能组件库文件都需要更新加载，且HCNetSDKCom文件夹名不能修改。

#### 2.3.2 hpr库

网络通讯库的依赖库，Linux SDK使用时和网络通讯库同时加载。

#### 2.3.3 RTSP通讯库

支持RTSP传输协议的网络库。当需要对支持RTSP协议的产品进行取流等操作时就必须
加载该项组件。

#### 2.3.3 转封装库

库的功能可以分为两种：一种是将标准码流转换成采用我们公司封装格式的码流。当用户
需要对支持RTSP协议的产品捕获采用本公司封装格式的码流数据时（即当设置NET_DVR_RealPlay_V40
接口中的回调函数捕获数据或者调用NET_DVR_SetRealDataCallBack接口捕获数据时）必须加载该组件。
另一种功能是能将标准码流转换成其他格式的封装，如3GPP、PS等。例如，当用户需要对支持RTSP
协议的产品实时捕获指定封装格式的码流数据（对应的SDK接口为NET_DVR_SaveRealData）时必须加
载该项组件。  

#### 2.3.5 语音对讲库

用于语音对讲时通过声卡采集数据并按照指定的编码格式编码码流或者解码播放音频码
流数据（不带封装格式的码流数据）。V4.2.2.5及以前版本SDK均采用windows API实现相关功能。之
后版本默认使用语音对讲库的方式，通过接口NET_DVR_SetSDKLocalCfg可以选择之前的windows API
模式。OpenAL32.dll 为依赖库，语音对讲库模式下必须加载。Windows64位或者Linux系统下无语音对
讲功能。

#### 2.3.6 字符转换库

电脑字符集和设备字符集不一致时，SDK内部需要进行字符编码转换，SDK默认使用libiconv
库进行类型转换。如果用户不想使用libiconv编码库，可以调用NET_DVR_SetSDKLocalCfg (类型: 
NET_SDK_LOCAL_CFG_TYPE_BYTE_ENCODE)设置字符转码回调函数，将用户自己的字符编码接口告知
SDK，然后SDK将使用用户提供的字符编码接口进行字符串处理。 

#### 2.3.7 模拟能力集

如果需要获取设备能力集（NET_DVR_GetDeviceAbility），建议调用NET_DVR_SetSDKLocalCfg启用模拟能力集，此时需要加载LocalXml.zip（要求和网络通讯库放在同一个目录下）。 

#### 2.3.8 帧分析库

用于分析视音频帧数据，调用NET_DVR_SetESRealPlayCallBack、NET_DVR_SetPlayBackESCallBack 设置裸码流回调函数等接口时，必须加载该库文件。 

#### 2.3.9 播放库

主要用于对实时码流数据进行解码显示（实现预览功能）和对录像文件进行回放解码等。用户如果需要在SDK内部进行对实时流和录像码流播放显示时（即NET_DVR_RealPlay_V40接口的第二个结构体参数的播放句柄设置成有效句柄时）必须加载该组件，而如果用户仅需要用网络通讯库捕获到数据后再外部自行处理就不需要加载该组件，这种情况下用户在外部自行解码将更灵活，可参见软解码库函数说明《播放器SDK编程指南》。 
