> 海康工业相机SDK+OpenCV实例(1)：https://blog.csdn.net/weixin_44229257/article/details/124387983

# 海康SDK和 OPENCV 的安装与编译

> 视觉项⽬⽤到海康威视的 ⼯业相机，选择MVS_SDK_V3_5_0_3_VC90_Runtime_STD作为配套⼯业相机⼆次开发的SDK Runtime组件包。配置C语⾔开发环
> 境，选择Visual Studio 2022；配置视觉算法库，选择OpenCV3.4.12版本。  
> 本⽂就SDK和OPENCV的安装与编译进⾏详解，后续会对SDK具体内容与OpenCV联合编程开发进⾏探讨。

## 1. 海康SDK安装

“海康威视”是中国领先的监控产品供应商，致⼒于不断提升视频处理技术和视频分析技术，⾯向全球提供领先的监控产品、技术解决⽅案与专业优质服务，为客⼾
持续创造最⼤价值。  
海康威视官⽹：https://www.hikvision.com/cn/  
海康威视SDK下载地址：https://www.hikrobotics.com/cn/machinevision/service/download?module=0  
下载版本：机器视觉⼯业相机SDK V3.5.0版本Runtime组件包（Windows）

## 2. OpenCV安装

这边不推荐利⽤Cmake进⾏编译安装，我们可以在OpenCV官⽹选择相应的版本进⾏下载。  
OpenCV官⽹：https://opencv.org/  
版本下载地址：https://opencv.org/releases/page/2/  
版本号：3.4.12

## 3. VS2022的OpenCV与SDK配置

这边需要同时对OpenCV和海康威视⼯业相机SDK进⾏库函数的配置，以⽅便后续的联合开发。

### 3.1 配置OpenCV

- ⾸先，将 **opencv 加⼊环境变量**，路径如下：  

  ```
  C:\Users\wenxi\opencv\build\x64\vc15\bin。
  ```

- 在对应项⽬的属性中，选择VC++⽬录的包含⽬录和库⽬录。  
  在包含⽬录加⼊OpenCV的安装路径：  

  ```
  C:\Users\wenxi\opencv\build\include\opencv2
  C:\Users\wenxi\opencv\build\include
  ```

  在库⽬录加⼊OpenCV的安装路径：

  ```
  C:\Users\wenxi\opencv\build\x64\vc15\lib
  ```

  ![VC++ 目录](https://i-blog.csdnimg.cn/blog_migrate/a56e085085c1d7317d1fc60d2d900b04.png)

- 在链接器输⼊项的**附加依赖项**加⼊OpenCV依赖库：  

  ```
  opencv_world3412d.lib
  opencv_world3412.lib
  ```

  **注意3412与版本号3.4.12的对应关系。**

### 3.2 配置海康威视SDK

- 在对应项⽬的属性中，选择 `C/C++` → `常规` 中的 `附加包含⽬录` 加⼊SDK的安装路径：  

  ```
  D:\Softwares\MVS\Development\Includes
  ```

  ![附加包含目录](https://i-blog.csdnimg.cn/blog_migrate/7de4b33cbc2fe646e17f5cc7f1ceb2d0.png)

- 在链接器的常规的**附加库⽬录**中，加⼊SDK的安装地址：

  ```
  D:\Softwares\MVS\Development\Libraries\win64
  ```

  ![附加库⽬录](https://i-blog.csdnimg.cn/blog_migrate/5c50378f31ecfc6d7f1fedd94892ab14.png)

- 在链接器输⼊项的附加依赖项加⼊SDK依赖库：

  ```
  MvCameraControl.lib
  ```

  
