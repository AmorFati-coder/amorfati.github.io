---
title: Pegsus和Taurus的无线互联问题
category: Harmony
tags: HiSpark
date: 2021-09-07
---



刚开始的话，主要看几个重要的函数就完事了(自己的C++确实学的稀烂)

<!--more-->

## T板的热点

- 切换到hiopenais框架下的build目录

```shell
make rebuild
make boards_rebuild
```

- 把编译生成的文件，复制到sdk下的hiopenais下

  ```
  cp -rf ./output/* ./sdk/hi3516dv300/osdrv/pub/rootfs_glibc/hiopenais/
  ```

- 改系统配置文件hiopenais.conf

  > 因为taurus.so和higo.so两个编译好的文件都需要调用LCD屏，注释一个，上面的strm_saver我也关掉了

  ```sh
  ; plug#序号只是在conf中区分不同的plug，序号可以不连续，但最大不超过10，hiopenais会枚举plug0~plug9
  [work_exts]
  plug0 = "exts/wifi.so"                   ; ap热点
  ;plug1 = "exts/taurus.so"        ; LCD
  plug2 = "exts/rtsp_agent.so"   ; RTSP
  plug3 = "exts/histreaming.so"  ; histreaming
  plug4 = "exts/ircutservice.so" ; ircut
  plug5 = "exts/higo.so"         ; UI
  
  [app]
  pre_start_vio = true ; 预先启动vi/vo
  ```

- 制作文件系统，烧录

## P板的连接

> 几个重要函数的调用

### 太长不看版

- 修改SSID的宏定义`#define CONFIG_AP_SSID  "HiSpark_XXXXX"`
### 简略版
```c++
#include<hi_wifi_api.h>
/*初始化*/
hi_wifi_init()
/*开启sta*/
hi_wifi_start_sta()
/*扫描热点*/
hi_wifi_sta_scan()

/*连接*/
 StaStartConnect()-> hi_wifi_sta_connect()
```

## 详细版

[Hi3861 WiFi操作，热点连接](https://blog.csdn.net/aa120515692/article/details/110087044)

