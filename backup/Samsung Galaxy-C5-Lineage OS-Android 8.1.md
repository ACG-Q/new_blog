> LineageOS14包来自[XDA](https://forum.xda-developers.com/android/development/rom-lineageos-14-1-samsung-galaxy-c5-t3585794)
>
> LineageOS15包来自[XDA](https://forum.xda-developers.com/android/development/rom-lineageos-15-1-samsung-galaxy-c5-t3828557)
>
> LineageOS16包来自[XDA](https://forum.xda-developers.com/android/development/unofficial-rom-lineageos-16-0-samsung-t3935875)

### 搞机必备

1. 下载本人打包好的文件

   [蓝奏云](https://www.lanzoux.com/i9z7y6b)
2. ~~以及C5000ZHU1CRJ5 五件套~~【仅限16和15】
3. 最后也就是ROM包
   [LineageOS 14.1 for Samsung Galaxy C5](https://vasy.ru/CM14/lineage-14.1-20170908-UNOFFICIAL-c5lte.zip)
   [LineageOS 15.1 for Samsung Galaxy C5](https://vasy.ru/LineageOS-15/c5lte/lineage-15.1-20190322-UNOFFICIAL-c5lte.zip)
   [LineageOS 16.0 for Samsung Galaxy C5](https://vasy.ru/LineageOS-16/c5lte/lineage-16.0-20191016-UNOFFICIAL-c5lte.zip)

**准备完成后，进入正题**

### 开始搞机

> 思路如下：解锁→刷TWRP→刷ROM包→完成！！

1. 安装CROM Service APK【三星解锁工具】
   - 路径：samsung_sm_c5000所有文件/CROM Service解锁/CROM Service[三星解锁].apk
   - <img src="https://s1.ax1x.com/2020/04/28/JISzLV.png" alt="CROM解锁" style="zoom: 33%;" />
2. 解除FRP 锁【也就是查找我的手机】

<!-- more -->

3. 进入Download模式【就是挖煤模式、线刷模式。方法如下：关机状态下，按 **音量下键+HOME键+电源键** 】，然后连接电脑
4. 打开Odin【三星刷机工具】
5. ~~刷入**港版C5000ZHU1CRJ5五件套**，待刷新重启后，进入手机升级页面，完成后，立即进入Download模式~~【**仅限刷16和15时**】
6. 选择AP，选择TWRP包【我收集了两个，都是3.2的，随便选一个就可以了】
   - <img src="https://s1.ax1x.com/2020/04/28/JIppZT.png" alt="操作" style="zoom: 67%;" />
   - <img src="https://s1.ax1x.com/2020/04/28/JISxs0.png" alt="结果" style="zoom:67%;" />
7. 按 **音量下键+电源键** 重启【大概6秒】，之后迅速，按下 **音量上键+HOME键+电源键** 【没按住，也不用慌，长按电源键，黑屏后，再重复之前的操作】进入TWRP界面
8. 安装 LineageOS 14/15/16.0 for Samsung Galaxy C5 ROM包

### 结果截图

- LineageOS 14.1

  - 14不配拥有图片
- LineageOS 15.1

  - <img src="https://s1.ax1x.com/2020/04/28/JIpsln.png" alt="rec" style="zoom: 33%;" />
  - <img src="https://s1.ax1x.com/2020/04/28/JIpyyq.png" alt="信息" style="zoom:33%;" />
- LineageOS 16.0

  - <img src="https://s1.ax1x.com/2020/04/28/JIpneK.png" alt="rec" style="zoom:33%;" />
  - <img src="https://s1.ax1x.com/2020/04/28/JIpeL6.png" alt="信息" style="zoom:33%;" />

### 扩展-五件套

**下载方法1：**

|        名称        |   C5000ZHU1CRJ5.7z   |
| :----------------: | :------------------: |
|      文件类型      |        压缩包        |
| Onedrive链接·短链 | http://t.cn/A67mEy7e |

**下载方法2：**

|   名称   |       C5000ZHU1CRJ5_C5000ZZH1CRJ5_TGY.zip       |
| :------: | :---------------------------------------------: |
| 文件类型 |                     压缩包                     |
| 百度链接 | https://pan.baidu.com/s/1lx0FMuwoZhGT1ucEiAu5mQ |
|  提取码  |                      8wx8                      |

**下载方法3(不推荐)：**

> 搞不懂这个网站到底让不让下载文件

|   名称   |                                    C5000ZHU1CRJ5_C5000ZZH1CRJ5_TGY.zip（可能）                                    |
| :------: | :----------------------------------------------------------------------------------------------------------------: |
| 文件类型 |                                                   压缩包（可能）                                                   |
| 外网链接 | [三星固件SAMMOBILE](https://www.sammobile.com/samsung/galaxy-c5/firmware/SM-C5000/TGY/download/C5000ZHU1CRJ5/246285/) |

### 后续故事

> 虽然不清楚你们会不会这个问题(电信卡无法打电话、收短信)

1. 先Root，我推荐使用面具(Magisk)
2. 安装ES文件管理器或者RE文件管理器
   - 打开 **/system/build.prop**
3. 找到**ro.telephony.default_network=1**
   - 将“1”改为“22”，“10“，”9”
4. 重启手机，之后重置WLAN,移动数据和蓝牙设置

> 亲测**LineageOS 16.0**无法通过这个方法修复电信不能打电话、接电话、收短信、发短信
<!-- ##{"timestamp":1583292897}## -->