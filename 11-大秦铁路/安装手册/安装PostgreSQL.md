# 货检的数据库环境

数据库采用免费、开源的 `PostgreSQL`，采用当前（2004年3月）最新的 `16.2-1` 的 X64 版本；但是客户端采用 32位 的 `9.6.24` 版本，是 32位 的最后一个版本。

## 1. 安装PostgreSQL

### 1.1 数据库安装

按照默认进行安装，安装过程中，需要填写的参数按照如下进行设置：

- 安装参数

  | 项目       | 数值     | 默认值 | 备注 |
  | ---------- | -------- | ------ | ---- |
  | 管理者口令 | HJ123456 |        |      |
  | 开放端口   | 15432    | 5432   |      |
  |            |          |        |      |

- 附加安装不要安装任何内容

  `EDB Language V4.2-1` 如果需要安装，可以采用线下安装的方式。

### 1.2 安装语言附件

没有语言附件的话，用 `pgAdmin 4` 管理数据库的时候，尤其是错误信息码，可能是乱码。可以选择安装 `EDB Language V4.2-1` 附件，当然也可以选择不安装，因为服务器端的操作比较少。

### 1.3 数据库语言

当前，PostgreSQL 在服务器端，仅仅支持 UTF-8，不能选择用 `GB18030` 或 `GBK` 内码，来创建数据库。

## 2. 创建数据库

用 `pgAdmin 4` 来创建货检专用数据表。

- 首次启动需要输入口令（安装时设置的管理者口令）：  
  ![image-20240330191903404](images/image-20240330191903404.png)

- 创建货检专用数据库  
  
  可以看到，PostgreSQL 在安装的时候，已经创建了默认的 Postgres 数据库，而我们需要创建 `HJAG` 的数据库。  
  
  在 `databases` 点击鼠标右键，选择 `create` → `database`
  ![image-20240330192349530](images/image-20240330192349530.png)
  
- 填写数据库名称  
  ![image-20240330192839833](images/image-20240330192839833.png)  
  点击 `save` 按钮，确认创建数据库。画面左侧的 `databases` 下面就会出现 HJAG 的数据库名称。
  
- 尝试创建数据表

  `databases` → `HJAG` → `Schemas` → `Tables` ，点击鼠标右键，可以选择用图形界面方式建立数据表，比较麻烦，还是采用 SQL 方式更加方便。

  选择弹出菜单里面的最后一项 `Query Tool` 来创建数据表。

  ```SQL
  DROP TABLE IF EXISTS test1;
  DROP SEQUENCE IF EXISTS test1_id_seq;
  CREATE SEQUENCE test1_id_seq;
  CREATE TABLE IF NOT EXISTS test1 (
      id INTEGER DEFAULT nextval('test1_id_seq') PRIMARY KEY,
      name varchar(10) NOT NULL,
      created TIMESTAMP,
      modified TIMESTAMP
  );
  ```

  这是单一主键的数据表，货检系统中，一般是多个主键的数据表

  ```SQL
  DROP TABLE IF EXISTS test1;
  DROP SEQUENCE IF EXISTS test1_id_seq;
  CREATE SEQUENCE test1_id_seq;
  CREATE TABLE IF NOT EXISTS test1 (
      id1 INTEGER NOT NULL,
      id2 INTEGER NOT NULL,
      name varchar(10) NOT NULL,
      created TIMESTAMP,
      modified TIMESTAMP,
      PRIMARY KEY ( id1, id2 )
  );
  ```

  创建数据表之后，点击画面左侧的 `Tables` ，鼠标右键选择 `Refresh` 刷新，选择具体的数据表，鼠标右键选择 `Propertity` 查看该数据表的属性。

## 3. 创建基础表

创建大秦铁路的接发车系统相关的数据表。

### 3.1 MA_DEV_MST

- 设备（股道）信息基础表

  ```sql
  DROP TABLE IF EXISTS MA_DEV_MST;
  CREATE TABLE IF NOT EXISTS MA_DEV_MST (
  	DEV_COD CHAR(10) NOT NULL,
  	DEV_NAM varchar(50) NOT NULL,
  	PRIMARY KEY ( DEV_COD )
  );
  
  ```
  
- 字段说明：
  
  |  #   | 字段名称 | 说明     | 主键 | 字段类型               | 备注                         |
  | :--: | -------- | -------- | ---- | ---------------------- | ---------------------------- |
  |  1   | DEV_COD  | 设备编号 | ✅    | 固定长度字符串：10字节 | 例：DSZ_CZ_SXX 大石庄-上行线 |
  |  2   | DEV_NAM  | 设备名称 |      | 可变字符串：50字节     |                              |
  
  更新用SQL（需要大秦铁路所有站进行统一编码）：
  
  ```sql
  DELETE FROM MA_DEV_MST;
  INSERT INTO MA_DEV_MST VALUES('DSZ_CZ_XXX', '大石庄-下行');
  INSERT INTO MA_DEV_MST VALUES('DSZ_CZ_SXX', '大石庄-上行');
  
  ```

### 3.2 MB_ODD_MST

- 异常名称数据表

  ```sql
  DROP TABLE IF EXISTS MB_ODD_MST;
  CREATE TABLE IF NOT EXISTS MB_ODD_MST (
  	ODD_COD INTEGER NOT NULL,
  	ODD_LVL INTEGER NOT NULL,
  	ODD_NAM varchar(50) NOT NULL,
  	PRIMARY KEY ( ODD_COD )
  );
  
  ```
  
- 字段说明：
  
  |  #   | 字段名称 | 说明     | 主键 | 字段类型           | 备注 |
  | :--: | -------- | -------- | ---- | ------------------ | ---- |
  |  1   | ODD_COD  | 异常号码 | ✅    | 数字               |      |
  |  2   | ODD_LVL  | 报警等级 |      | 数字               |      |
  |  3   | ODD_NAM  | 异常名称 |      | 可变字符串：50字节 |      |
  
- 更新用SQL：
  
  ```sql
  DELETE FROM MB_ODD_MST;
  INSERT INTO MB_ODD_MST VALUES(1, 2, '车门开启');
  INSERT INTO MB_ODD_MST VALUES(2, 2, '车窗开启');
  INSERT INTO MB_ODD_MST VALUES(3, 2, '悬挂异物');
  INSERT INTO MB_ODD_MST VALUES(4, 2, '货物撒漏');
  INSERT INTO MB_ODD_MST VALUES(5, 2, '声音异常');
  INSERT INTO MB_ODD_MST VALUES(6, 2, '篷布飘起');
  INSERT INTO MB_ODD_MST VALUES(7, 2, '尾部软管未吊起');
  INSERT INTO MB_ODD_MST VALUES(8, 2, '搭扣异常');
  INSERT INTO MB_ODD_MST VALUES(9, 2, '闲杂人员扒乘');
  
  ```

### 3.3 MC_STA_MST

- 数据库信息表（车站前后站）

  ```sql
  DROP TABLE IF EXISTS MC_STA_MST;
  CREATE TABLE IF NOT EXISTS MC_STA_MST (
  	STA_IDX INTEGER NOT NULL,
  	STA_NAM varchar(20) NOT NULL,
  	ftp_host varchar(20) NOT NULL,
  	ftp_data varchar(20) NOT NULL,
  	ftp_user varchar(20) NOT NULL,
  	ftp_password varchar(20) NOT NULL,
  	ftp_port INTEGER NOT NULL,
  	PRIMARY KEY ( STA_IDX )
  );
  
  ```

- 字段说明：
  
  |  #   | 字段名称     | 说明         | 主键 | 字段类型           | 备注             |
  | :--: | ------------ | ------------ | ---- | ------------------ | ---------------- |
  |  1   | STA_IDX      | 车站编号     | ✅    | 数字               | 1：前站 2：后站  |
  |  2   | STA_NAM      | 车站名称     |      | 可变字符串：20字节 |                  |
  |  3   | ftp_host     | 服务器IP地址 |      | 可变字符串：20字节 |                  |
  |  4   | ftp_data     | 数据目录     |      | 可变字符串：20字节 | 默认：`hjs_data` |
  |  5   | ftp_user     | 登录用户名   |      | 可变字符串：20字节 |                  |
  |  6   | ftp_password | 登录口令     |      | 可变字符串：20字节 |                  |
  |  7   | ftp_port     | 开放端口     |      | 数字               | 货检默认：`1021` |
  
- 更新SQL

  ```SQL
  DELETE FROM MC_STA_MST;
  INSERT INTO MC_STA_MST VALUES(1, '前站名', '192.168.114.173', 'hjs_data_p', 'FTP_USER', 'hj123456', 1021);
  INSERT INTO MC_STA_MST VALUES(2, '后站名', '192.168.114.173', 'hjs_data_n', 'FTP_USER', 'hj123456', 1021);
  
  ```

  ※ 该数据表只有 2 条记录。

### 3.4 MD_STA_MST

车务段所用的车站信息表。

- 车站信息表（车务段用）

  ```sql
  DROP TABLE IF EXISTS MD_STA_MST;
  CREATE TABLE IF NOT EXISTS MD_STA_MST (
  	STA_COD CHAR(3) NOT NULL,
  	STA_NAM varchar(20) NOT NULL,
  	ftp_host varchar(20) NOT NULL,
  	ftp_data varchar(20) NOT NULL,
  	ftp_user varchar(20) NOT NULL,
  	ftp_password varchar(20) NOT NULL,
  	ftp_port INTEGER NOT NULL,
  	PRIMARY KEY ( STA_COD )
  );
  
  ```

- 字段说明：
  
  |  #   | 字段名称     | 说明         | 主键 | 字段类型              | 备注              |
  | :--: | ------------ | ------------ | ---- | --------------------- | ----------------- |
  |  1   | STA_COD      | 车站编号     | ✅    | 固定长度字符串：3字节 | 例：DSZ 大石庄    |
  |  2   | STA_NAM      | 车站名称     |      | 可变字符串：20字节    |                   |
  |  3   | ftp_host     | 服务器IP地址 |      | 可变字符串：20字节    |                   |
  |  4   | ftp_data     | 数据目录     |      | 可变字符串：20字节    | 默认：`HJAG`      |
  |  5   | ftp_user     | 登录用户名   |      | 可变字符串：20字节    |                   |
  |  6   | ftp_password | 登录口令     |      | 可变字符串：20字节    |                   |
  |  7   | ftp_port     | 开放端口     |      | 数字                  | 货检默认：`15432` |
  
- 更新SQL

  ```SQL
  DELETE FROM MD_STA_MST;
  INSERT INTO MD_STA_MST VALUES('DSZ', '前站名', '192.168.114.173', 'hjs_data', 'FTP_USER', 'hj123456', 1021);
  
  ```
  
  ※ 大秦铁路一共有 7 个车站安装了 `接发车系统`，所以应该插入 7 条数据。

## 4. 创建信息表

### 4.1 TC_TRN_TBL

- 列车信息数据表

  ```sql
  DROP TABLE IF EXISTS TC_TRN_TBL;
  CREATE TABLE IF NOT EXISTS TC_TRN_TBL (
  	TRN_YMD CHAR(15) NOT NULL,
  	DEV_COD CHAR(10) NOT NULL,
  	TRN_COD varchar(5) NOT NULL,
  	PAS_TMS INTEGER,
  	CAR_SPD varchar(20),
  	CAR_SP2 INTEGER,
  	CAR_LEN INTEGER NOT NULL,
  	CAR_ALL INTEGER NOT NULL,
  	CAR_CNT INTEGER NOT NULL,
  	ENG_CNT INTEGER NOT NULL,
  	IOB_CAR_ALL INTEGER NOT NULL,
  	IOB_ENG_CNT INTEGER NOT NULL,
  	IOB_CAR_CNT INTEGER NOT NULL,
  	STR_CAR_ALL INTEGER NOT NULL,
  	STR_ENG_CNT INTEGER NOT NULL,
  	STR_CAR_CNT INTEGER NOT NULL,
  	NVR_CNT INTEGER NOT NULL,
  	MP3_CNT INTEGER NOT NULL,
  	HOT_CNT INTEGER NOT NULL,
  	CAR_INI varchar(50),
  	STA_FLG SMALLINT DEFAULT 0,
  	TRN_MEM varchar(50),
  	DEL_FLG SMALLINT DEFAULT 0,
  	PRIMARY KEY ( TRN_YMD, DEV_COD )
  );
  
  ```
  
- 字段说明：
  
  |  #   | 字段名称    | 说明                                | 主键 | 字段类型                   | 备注                                               |
  | :--: | ----------- | ----------------------------------- | ---- | -------------------------- | -------------------------------------------------- |
  |  1   | TRN_YMD     | 列车通过开始时刻（YYYYMMDD-HHmmSS） | ✅    | 固定长度字符串：15字节     |                                                    |
  |  2   | DEV_COD     | 设备编码（线路编号）                | ✅    | 固定长度字符串：10字节     | 例：DSZ_CZ_SXX 大石庄-上行线<br />参照：MA_DEV_MST |
  |  3   | TRN_COD     | 车次号码                            |      | 可变长度字符串：5字节      |                                                    |
  |  4   | PAS_TMS     | 通过时长                            |      | 整数                       | 单位：毫秒                                         |
  |  5   | CAR_SPD     | 通过速度                            |      | 可变长度字符串：20字节     | 速度描述                                           |
  |  6   | CAR_SP2     | 通过速度                            |      | 整数                       |                                                    |
  |  7   | CAR_LEN     | 车辆换长合计                        |      | 整数                       | 换长合计的10倍                                     |
  |  8   | CAR_ALL     | 车辆数量（货车 + 机车）             |      | 整数                       |                                                    |
  |  9   | CAR_CNT     | 货车数量                            |      | 整数                       |                                                    |
  |  10  | ENG_CNT     | 机车数量                            |      | 整数                       |                                                    |
  |  11  | IOB_CAR_ALL | 车位车辆数量（货车 + 机车）         |      | 整数                       |                                                    |
  |  12  | IOB_ENG_CNT | 车位机车数量                        |      | 整数                       |                                                    |
  |  13  | IOB_CAR_CNT | 车位货车数量                        |      | 整数                       |                                                    |
  |  14  | STR_CAR_ALL | 车号车辆数量（货车 + 机车）         |      | 整数                       |                                                    |
  |  15  | STR_ENG_CNT | 车号机车数量                        |      | 整数                       |                                                    |
  |  16  | STR_CAR_CNT | 车号货车数量                        |      | 整数                       |                                                    |
  |  17  | NVR_CNT     | 面阵图像数量                        |      | 整数                       |                                                    |
  |  18  | MP3_CNT     | 音频文件数量                        |      | 整数                       |                                                    |
  |  19  | HOT_CNT     | 轴温数据文件数量                    |      | 整数                       |                                                    |
  |  20  | CAR_INI     | 配置文件名称                        |      | 可变长度字符串：最大50字节 |                                                    |
  |  21  | STA_FLG     | 本站、下站处理标志                  |      | 短整数                     | 0：本站处理 1：下站处理                            |
  |  22  | TRN_MEM     | 备注                                |      | 可变长度字符串：最大50字节 |                                                    |
  |  23  | DEL_FLG     | 删除标志                            |      | 短整数                     | 0：未删除 1：删除                                  |

### 4.2 TD_CAR_TBL

- 车辆数据信息表

  ```sql
  DROP TABLE IF EXISTS TD_CAR_TBL;
  CREATE TABLE IF NOT EXISTS TD_CAR_TBL (
  	TRN_YMD CHAR(15) NOT NULL,
  	DEV_COD CHAR(10) NOT NULL,
  	CAR_IDX INTEGER NOT NULL,
  	RFD_COD varchar(25),
  	CAR_COD varchar(7),
  	ENG_FLG INTEGER,
  	CHK_FLG INTEGER,
  	DEL_FLG INTEGER,
  	PRIMARY KEY ( TRN_YMD, DEV_COD, CAR_IDX )
  );
  ```
  
- 字段说明：
  
  |  #   | 字段名称 | 说明                                | 主键 | 字段类型               | 备注                  |
  | :--: | -------- | ----------------------------------- | ---- | ---------------------- | --------------------- |
  |  1   | TRN_YMD  | 列车通过开始时刻（YYYYMMDD-HHmmSS） | ✅    | 固定长度字符串：15字节 |                       |
  |  2   | DEV_COD  | 设备编码                            | ✅    | 固定长度字符串：10字节 | 参照：MA_DEV_MST      |
  |  3   | CAR_IDX  | 车辆编号                            |      | 整数                   |                       |
  |  4   | RFD_COD  | 电子标签                            |      | 可变长度字符串：25字节 | 正常是20字节          |
  |  5   | CAR_COD  | 车号                                |      | 可变长度字符串：7字节  | 机车4字节、货车7字节  |
  |  6   | ENG_FLG  | 机车标志                            |      | 数字                   | 0：货车 1：机车       |
  |  7   | CHK_FLG  | 已检标志                            |      | 整数                   | 0：未检 1：已检       |
  |  8   | DEL_FLG  | 处理状态                            |      | 数字                   | 0：未处理 1：处理结束 |

### 4.3 TE_ODD_TBL

- 异常信息数据表

  ```sql
  DROP TABLE IF EXISTS TE_ODD_TBL;
  CREATE TABLE IF NOT EXISTS TE_ODD_TBL (
  	TRN_YMD CHAR(15) NOT NULL,
  	DEV_COD CHAR(10) NOT NULL,
  	CAR_IDX INTEGER NOT NULL,
  	LAC_IDX INTEGER NOT NULL,
  	ODD_COD INTEGER NOT NULL,
  	ODD_FLG INTEGER NOT NULL,
  	ERR_FLG INTEGER,
  	DEL_FLG INTEGER,
  	PRIMARY KEY ( TRN_YMD, DEV_COD, CAR_IDX, LAC_IDX )
  );
  ```
  
- 字段说明：
  
  |  #   | 字段名称 | 说明                                | 主键 | 字段类型               | 备注                      |
  | :--: | -------- | ----------------------------------- | ---- | ---------------------- | ------------------------- |
  |  1   | TRN_YMD  | 列车通过开始时刻（YYYYMMDD-HHmmSS） | ✅    | 固定长度字符串：15字节 |                           |
  |  2   | DEV_COD  | 设备编码                            | ✅    | 固定长度字符串：10字节 | 参照：MA_DEV_MST          |
  |  3   | CAR_IDX  | 车辆编号                            | ✅    | 数字                   |                           |
  |  4   | LAC_IDX  | 相机编号                            | ✅    | 数字                   |                           |
  |  5   | ODD_COD  | 异常编号                            |      | 数字                   | 参照：MB_ODD_MST          |
  |  6   | ODD_FLG  | 报告标志                            |      | 数字                   | 0：人工标定 1：AI智能识别 |
  |  7   | ERR_FLG  | 误报标志                            |      | 数字                   | 0：未定 1：误报 2：非误报 |
  |  8   | DEL_FLG  | 处理状态                            |      | 数字                   | 0：未处理 1：处理结束     |

## 4. 开放对外服务

- 默认只对本机开放，修改 `C:\Program Files\PostgreSQL\16\data\pg_hba.conf` ，修改之后需要重启数据库的相关服务。

  找到如下内容（`IPv4 local connections`）：

  ```
  # TYPE  DATABASE        USER            ADDRESS                 METHOD
  # IPv4 local connections:
  host    all             all             127.0.0.1/32            scram-sha-256
  
  ```

  上述内容后面追加如下行：

  ```bash
  ## 允许 IP 地址从 192.168.0.1 到 192.168.0.254 的主机（客户端）。
  host all all 192.168.0.0/24 scram-sha-256
  ```

  本人虚拟机的实际情况（需要根据实际网络情况，进行设置）：

  ```
  host all all 192.168.114.0/24 scram-sha-256
  ```

- 对外开放数据库端口

  打开防火墙，在 `入站规则` 增加 `PostgreSQL` 的端口号（默认5432，货检系统采用 54321）。
