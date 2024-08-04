> 海康工业相机SDK + OpenCV实例(3)：http://t.csdnimg.cn/MDyQz

# 相机初始化 Bug 调试

> 前⽂[海康工业相机SDK+OpenCV实例(2)：RawDataFormatConvert详解](https://blog.csdn.net/weixin_44229257/article/details/124407308?spm=1001.2014.3001.5502)中，以RawDataFormatConvert为例，讲解了海康 [工业相机](https://so.csdn.net/so/search?q=%E5%B7%A5%E4%B8%9A%E7%9B%B8%E6%9C%BA&spm=1001.2101.3001.7020) 的基本操作与转为 Mat 格式的操作。但是在实际应⽤中，相机开关初始化遇到了种种不便利的情况。
>
> - 调试时期，相机默认的心跳时间为60s，导致重启周期很⻓。
> - 当相机已经打开或者连接，再次访问，导致开机报错。
>
> 基于上述可能存在的问题，本⽂进⾏调试。

## 1. 心跳时间设置

相机在调试过程，在程序中，若相机已经开机，如果在程序异常退出前相机没有进⾏相应的关机操作，会导致⼀段时间内相机⽆法连接，这个时间就叫**心跳时间**。海康相机默认的心跳时间为 60s，这意味着如果程序异常跳出，海康相机 60s 内不能重新进⾏连接，这会给相机的调试带来困扰。  

- 海康工业相机SDK提供了心跳时间 `HeartBeatTimeout` 设置的接口 `MV_CC_SetHeartBeatTimeout`，可以在相机开机后加⼊下述程序段，设置心跳时间为 `3s`（心跳时间的最⼩值为 `500ms`）。  

  ```c++
  int HeartBeatTimeout = 3000;
  nRet = MV_CC_SetHeartBeatTimeout(handle, HeartBeatTimeout);
  if (MV_OK != nRet)
  {
      printf("Set HeartBeatTimeout fail! nRet [0x%x]\n", nRet);
  }
  ```

- 尽管重新设置了心跳时间，但也不排除在极短的心跳时间内，程序需要重新启动。我们可以通过⼀个判断设备是否可达的接口 `MV_CC_IsDeviceAccessible`，倘若当前相机仍然处于心跳时间内，程序会⾃动等待心跳时间。  

  ```c++
  nRet = MV_CC_IsDeviceAccessible(stDeviceList.pDeviceInfo[nIndex], MV_ACCESS_Exclusive);
  if (nRet == 0) Sleep(3000);
  ```

  MV_ACCESS_Exclusive是⼀种相机访问模式，具体种类可详⻅于SDK。

## 2. 相机先关闭再打开

当相机已经在服务端被打开，客⼾端初次连接后，倘若客⼾端异常跳出，没有下达相机关闭指令，当客⼾端再次尝试连接相机时，由于相机已经打开，会有相机开机异常的报错。  

- 因此，我们可以尝试在每次连接相机之前，先调⽤判断相机连接状态的接口`MV_CC_IsDeviceConnected`，倘若相机已经连接，尝试先关闭再打开相机；倘若，相机没有连接，直接打开相机。  

  ```c++
  if (nRet == 1)
  {
      nRet = MV_CC_CloseDevice(handle);
      if (MV_OK != nRet)
      {
          printf("ClosDevice fail! nRet [0x%x]\n", nRet);
      }
  }
  //创建句柄，打开设备
  nRet = MV_CC_CreateHandle(&handle, stDeviceList.pDeviceInfo[nIndex]);
  if (MV_OK != nRet)
  {
      printf("Create Handle fail! nRet [0x%x]\n", nRet);
  }
  nRet = MV_CC_OpenDevice(handle);
  if (MV_OK != nRet)
  {
      printf("Open Device fail! nRet [0x%x]\n", nRet);
  }
  ```

  

