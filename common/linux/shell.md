<!--
 * @Author: liu kang
 * @Date: 2023-04-21 12:23:59
 * @LastEditors: LiuKang
 * @LastEditTime: 2023-04-21 12:24:05
 * @FilePath: \Notes\common\linux\shell.md
 * @Description: 
 * 
 * Copyright (c) 2023 by ${git_name_email}, All Rights Reserved. 
-->

# Common

## [tar](https://blog.csdn.net/example440982/article/details/51712973)

tar -cvf test.tar test  #仅打包，不压缩
tar -zcvf test.tar.gz test  #打包后，以gzip压缩 在参数f后面的压缩文件名是自己取的，习惯上用tar来做，如果加z参数，则以tar.gz 或tgz来代表gzip压缩过的tar file文件
tar -zcvf dist.tar.gz -C dist .  #注意最后有一点，通过 -C 参数指定目录，可以去掉打包内容包含相对目录，打包内容只包含 dist/* 下的文件内容，不包含 dist 目录

tar -zxvf 解压文件到当前文件夹

---

## [查看磁盘空间](https://www.runoob.com/w3cnote/linux-view-disk-space.html)

1.df

df 以磁盘分区为单位查看文件系统，可以获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

例如，我们使用**df -h**命令来查看磁盘信息， **-h** 选项为根据大小适当显示：

  ![1682065290900](image/shell/1682065290900.png)

```

```

2.du

**du** 的英文原义为  **disk usage** ，含义为显示磁盘空间的使用情况，用于查看当前目录的总大小

```

du -sh 查看当前目录的大小
du log2012.log  显示指定文件所占空间
du -h test 方便阅读的格式显示test目录所占空间情况

-s：对每个Names参数只给出占用的数据块总数。
-a：递归地显示指定目录中各文件及子目录中各文件占用的数据块数。若既不指定-s，也不指定-a，则只显示Names中的每一个目录及其中的各子目录所占的磁盘块数。
-b：以字节为单位列出磁盘空间使用情况（系统默认以k字节为单位）。
-k：以1024字节为单位列出磁盘空间使用情况。
-c：最后再加上一个总计（系统默认设置）。
-l：计算所有的文件大小，对硬链接文件，则计算多次。
-x：跳过在不同文件系统上的目录不予统计。
-h：以K，M，G为单位，提高信息的可读性。

```

---

## [find](https://www.runoob.com/linux/linux-comm-find.html)

find 命令用于在指定目录下查找文件和目录

find [path][expression]

```
参数说明 :

path 是要查找的目录路径，可以是一个目录或文件名，也可以是多个路径，多个路径之间用空格分隔，如果未指定路径，则默认为当前目录。

expression 是可选参数，用于指定查找的条件，可以是文件名、文件类型、文件大小等等。

expression 中可使用的选项有二三十个之多，以下列出最常用的部份：

-name pattern：按文件名查找，支持使用通配符 * 和 ?。
-type type：按文件类型查找，可以是 f（普通文件）、d（目录）、l（符号链接）等。
-size [+-]size[cwbkMG]：按文件大小查找，支持使用 + 或 - 表示大于或小于指定大小，单位可以是 c（字节）、w（字数）、b（块数）、k（KB）、M（MB）或 G（GB）。
```

e.g

find dir -name  " " |  xargs rm  #查找并删除指定名字文件

## [ps](https://www.yiibai.com/linux/ps.html)

```
a  显示所有进程
-a 显示同一终端下的所有程序
-A 显示所有进程
c  显示进程的真实名称
-N 反向选择
-e 等于“-A”
e  显示环境变量
f  显示程序间的关系
-H 显示树状结构
r  显示当前终端的进程
T  显示当前终端的所有程序
u  指定用户的所有进程
-au 显示较详细的资讯
-aux 显示所有包含其他使用者的行程
 -C<命令> 列出指定命令的状况
--lines<行数> 每页显示的行数
--width<字符数> 每页显示的字符数
--help 显示帮助信息-
-version 显示版本显示

```

e.g.1

ps -ef # 显示所有进程信息，连同命令行

ps -ef|grep ssh #ps 与grep 常用组合用法，查找特定进程


---
