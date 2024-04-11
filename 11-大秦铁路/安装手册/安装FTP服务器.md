# 安装FTP服务器

大秦铁路的货检系统的服务器采用 Windows10 系统，所以直接利用 Windows 的 `FTP功能`。

## 1. 安装FTP功能

- 在 `应用和功能` 界面的右侧，点击 `程序和功能`，在显示出来的 `程序和功能` 画面的左侧，点击 `启用和关闭 Windows 功能`。  
  ![image-20240330204404919](images/image-20240330204404919.png)

- 找到 `Internet Information Services` 项目，展开后，选择 `FTP 服务器` 的相应功能  
  ![image-20240330204610561](images/image-20240330204610561.png)  
  点击 `确定` 按钮，保存设置后，系统会自动追加相应功能。  
  ![image-20240330204833977](images/image-20240330204833977.png)  
  安装结束之后，显示如下画面：  
  ![image-20240330204921087](images/image-20240330204921087.png)  
  点击 `关闭` 按钮，完成 FTP服务器 的功能追加。

安装结束之后，需要重新启动电脑，或者是重启 `IIS服务`。

## 2. 调整FTP站点

### 2.1 添加FTP站点

所在位置：`控制面板` → `系统和安全` → `管理工具` → `Internet Information Services (IIS)管理器`

- 添加 FTP 站点  
  ![image-20240330210424974](images/image-20240330210424974.png)  

- 设置货检FTP的相关内容：  
  ![image-20240330210622668](images/image-20240330210622668.png)  
  站点名称：`HJAG_FTP`  
  物理路径：`C:\inetpub\ftproot`

- 绑定和SSL设置  
  ![image-20240330211123728](images/image-20240330211123728.png)  
  端口：`1021`  
  SSL：`无SSL`

- 身份验证和授权信息  
  ![image-20240330211422440](images/image-20240330211422440.png)  
  身份验证：`基本`  
  允许访问：`所有用户`  
  权限：`读取` （根目录不设置写入权限)

- 添加结束后，可以看到如下内容：  
  ![image-20240330211819578](images/image-20240330211819578.png)

### 2.2 添加虚拟目录

- 在 `HJAG` 的站点上点击鼠标右键，选择 `添加虚拟目录`，显示如下画面：  
  别名：`HJS_DATA`  
  物理路径：`D:\HJS_DATA`  
  ![image-20240330214347537](images/image-20240330214347537.png)  
  点击 `确定` 按钮，保存设置。
  
- 修改授权规则  
  点击虚拟目录的名字，在中间画面双击 `FTP授权规则`，权限继承自根目录的 `读写` 权限，增加 `写入` 权限。  
  ![image-20240412001649581](images/image-20240412001649581.png)

## 3. 添加FTP用户

### 3.1 普通FTP用户

该用户在 货检客户端 使用，不具有 写入 权限。

- 添加 `普通FTP用户`  
  用户名：`FTP_USER`  
  密码：`hj123456`  
  ![image-20240330220058070](images/image-20240330220058070.png)  
  
- 设置权限

  需要在 `hjs_data` 目录的 `属性` → `安全` 设置里面，增加该用户，只是该用户只有普通的读写权限而已。  
  ![image-20240409222421246](images/image-20240409222421246.png)  

### 3.2 更新FTP用户

该用户在 货检数据上传 模块使用，具有`写`权限。

- 添加 `更新FTP用户`  
  用户名：`FTP_UPLOAD`  
  密码：`HJ202404`  
  ![image-20240330220618285](images/image-20240330220618285.png)  

- 修改权限  
  因为添加的 `FTP_UPLOAD`  用户位普通的 `user` 用户，需要把 `HJS_DATA` 目录的权限进行调整：  
  添加 `FTP_UPLOAD` 用户后，赋予该用户的 `修改` 权限  
  ![image-20240330221053661](images/image-20240330221053661.png)    

## 3. 调试FTP服务

采用 Windows 自带的 FTP客户端 工具进行调试。

因为端口没有采用默认的 21 端口号，所以需要在 命令提示符 内输入 ftp 指令后，用 `open localhost 1021` （注意：HOST和端口号之间用 半角空格 隔开）来连接 FTP服务。

### 3.1 在服务器进行调试

- 命令执行一览：  

  ```cmd
  C:\Users\liu>ftp
  ftp> open localhost 1021
  连接到 DESKTOP-SPBEP1T。
  220 Microsoft FTP Service
  200 OPTS UTF8 command successful - UTF8 encoding now ON.
  用户(DESKTOP-SPBEP1T:(none)): FTP_USER
  331 Password required
  密码:
  230 User logged in.
  ftp> cd hjs_data
  250 CWD command successful.
  ftp> ls
  200 EPRT command successful.
  125 Data connection already open; Transfer starting.
  HJS_DATA.ini
  226 Transfer complete.
  ftp: 收到 17 字节，用时 0.02秒 1.06千字节/秒。
  ftp> dir
  200 EPRT command successful.
  125 Data connection already open; Transfer starting.
  03-30-24  09:14PM                    0 HJS_DATA.ini
  226 Transfer complete.
  ftp: 收到 56 字节，用时 0.00秒 56000.00千字节/秒。
  ftp> bye
  221 Goodbye.
  
  ```

- 如果把上面的命令中的 `localhost` 修改为 真实的 IP地址，会出现如下 防火墙 的画面，选在 `专用网络` 和 `共用网络` 两项，点击 `允许访问` 按钮进行确认。  
  ![image-20240330221802050](images/image-20240330221802050.png)  
  ※ 该界面会在软件调试过程中，多次出现。

如上设置之后，针对服务器本机，FTP服务 已经可以正常访问，但是从其它电脑尚无法进行访问。

### 3.2 开启防火墙的FTP端口

`控制面板` → `系统和安全` → `Windows Defender 防火墙`

- 点击画面左侧的 `允许应用或功能通过 Windows Defender 防火墙` ，找到 `FTP 服务器` 项目：  
  `专用` 和 `公用` 的2个项目都选择上  
  ![image-20240330223331422](images/image-20240330223331422.png)  

- 开放 FTP服务的端口  
  `高级设置` → `入站规则` → `新建规则`   
  开启端口（TCP）：`1021`  
  名称：`货检FTP服务`  
  ![image-20240330224347342](images/image-20240330224347342.png) 

在开启了该端口之后，才能够从远处的电脑，对 货检FTP服务 进行访问。

### 3.3 验证设置效果

使用 `FTP_UPLOAD` 用户登录 FTP服务器，尝试用 `mkdir` `put` `get` 等指令，确认权限设置正常。

## 4. 货检数据配置

在 FTP服务 的数据目录下面，需要配置如下 2 个文件：

- HJConfig.sys
  客户端车站、股道等信息的配置文件

  ```ini
  [STA_NAM]
  DSZ=大石庄
  
  [IN__OUT]
  CZ=车站
  
  [ZZB_STK]
  DSZ_CZ_XXX=大石庄-下行
  DSZ_CZ_SXX=大石庄-上行
  
  ```

- DBConfig.sys

  数据库信息配置文件

  ```ini
  [SVR_CFG]
  SVR_IPA=192.168.114.173
  SVR_USR=postgres
  SVR_PWD=HJ123456
  SVR_PRT=15432
  
  ```

  > 一般来说，数据库名称不应该出现在该配置文件中，登录用户名也不应该是默认的 `postgres`，可能会因为设置不当，而导致泄密，不过因为是内部网络，所以不需要过分担心。

## 5. 确认连接正常

- 所有的设置结束之后，在 资源管理器 的地址里面输入一下地址，确认可以正确访问：

  ```
  ftp://FTP_USER:hj123456@192.168.114.173:1021/hjs_data/HJConfig.sys
  ```

- 上传数据之后，在 资源管理器 的地址里面输入一下地址，确认可以正确访问：

  ```
  ftp://FTP_USER:hj123456@192.168.114.173:1021/hjs_data/DSZ_CZ_SXX/20240327/20240327-000248-74316/20240327-000248-74316.ini
  ```

如果不能正确访问，说明权限设置存在一定问题，请联系计数人员协助解决。

## 6. 疑难解析

### 6.1 客户端连接时出现 `不明错误`

- **问题描述**  
  数据上传正常，说明 FTP服务 设置没有问题。  
  但是，客户端 `连接测试` 的时候，出现 `不明错误`，导致无法正常设置数据路径，从而无法启动客户端界面。  
  而客户端连接其它的 FTP服务 并没有问题。

- **问题分析**  
  首先可以确定的是，客户端软件本身并没有问题。  
  如果是 界面上的 `FTP服务` 的连接参数设置错误的话，这些都是能够正常抓取到的错误，能够正确报告错误的性质。  
  但是，如果是 `HJConfig.sys` 和 `DBConfig.sys` 的内容读取过程中出现错误的话，就会显示为 `不明错误`。
  
- **解决办法**  
  分析 `HJConfig.sys` 和 `DBConfig.sys` 的内容，确定哪里存在不正常的写法。  
  注：理论上上述 2 个文件的编码格式可以是 UTF8 和 ANSI 格式，软件会在读取数据后，自动把 UTF8 的编码，转换为 ANSI 编码。
  
- **问题根源**  
  所有行数据的前面都有 2 个半角空格，这个会影响到数据的正确认识。  
  软件只考虑了行数据的右侧会不经意的出现空格的区块，并没有考虑到左侧也会认为的添加空格；这个不符合 INI 文件的书写规范。  
  ※ 技术是非常死板的，必须按照技术规格来，否则不经意的问题，会引起巨大的麻烦；并且如上述问题一样，人眼很难在现场看出有啥问题。
