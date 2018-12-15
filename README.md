# AndroidDisk

包含项目描述，参考文献，项目计划，会议总结，项目进展等内容。

---
- [工作流程](#工作流程)
  - [11/15 工作总结](#1115-工作总结)
  - [11/30 工作总结](#1130-工作总结)
  - [12/15 工作总结](#1215-工作总结)
- [项目描述](#项目描述)
  1. [设计背景](#1-设计背景)
  2. [设计目标（功能描述）](#2-设计目标功能描述)
  3. [关键技术](#3-关键技术)
     1. [手机端app设计](#31-手机端app设计)
     2. [pc端小程序设计](#32-pc端小程序设计)
- [项目计划](#项目计划)
- [会议总结](#会议总结)
  1. [10/26 第一次会议](#1026-第一次会议)
  2. [10/30 第二次会议](#1030-第二次会议)

---

## 工作流程

### 11/15 工作总结

- 根据导师的建议，增添了相应的需求：PC端自动连接功能。并对应修改了[项目描述](#项目描述)与[项目计划](#项目计划)
- 第一阶段的工作任务较为简单，通过搜索相关资料，发现`apache ftp server`相较于`swiftp`相关资源较多，实现起来较为简单，于是最终敲定使用`apache ftp server`完成毕业设计
- 辞去工作时间996的实习工作，以便能更投入毕业设计
- 接下来两周我将把具有基本功能的完整app编写debug完毕

### 11/30 工作总结

　**完成部分：**

1. 学习了MVP架构，并以此架构编写毕业设计

   <center><img src="README_images\MVP.png" style="zoom:50%"><img src="README_images\mvp1.png" style="zoom:50%"></center>

2. 基本完成了FTP demo，实现的功能如下：

   1. 根据wifi情况自动获取ip，并提示用户
   2. 开启与关闭FTP

   |                开启FTP                |                FTP关闭状态切换Wifi                |               FTP开启状态下切换Wifi               |
   | :-----------------------------------: | :-----------------------------------------------: | :-----------------------------------------------: |
   | <img src="README_images\开启FTP.gif"> | <img src="README_images\FTP关闭状态切换Wifi.gif" style="zoom:50%"> | <img src="README_images\FTP开启状态切换Wifi.gif" style="zoom:50%"> |

　**下一次完成部分**

1. 编写配置界面，设置口令密码、端口、传输速率等

2. 添加自定义共享文件夹功能（难点）

   1. ftp是以一个目录作为服务器的根目录，而不能设置选取多个文件夹作为共享目录

   2. 可以尝试使用类似于Windows的快捷方式方法，将需要共享的文件夹创建快捷方式放在服务器根目录

      <img src="README_images\自定义共享文件夹解决方案.png">

3. 重新设计MainActivity，以适配第二个功能

### 12/15 工作总结

　**发现的问题**

1. Android文件系统的快捷方式十分难以实现，现阶段无法找到有效的资料

2. 经过开题报告，我修改了本次毕设的需求，力求实现一个AndroidDisk，能够方便的“挂载”在Windows系统上。

   挂载的要求有：方便传输、方便打开、方便修改

   1. FTP协议在传输上比较方便

   2. 然而FTP在打开文件时会走HTTP协议，即无法直接在Client(Android)端打开，比较不方便

      |          打开pdf等浏览器支持的文件           |         打开excel等浏览器不支持的文件          |
      | :------------------------------------------: | :--------------------------------------------: |
      | <img src="README_images\FTP打开pdf文件.png"> | <img src="README_images\FTP打开excel文件.png"> |
      |     直接在浏览器打开，且不能修改打开方式     |                 在浏览器中下载                 |

   3. 且FTP协议不能直接修改Server(Android)上的文件，除非先拷到Client(Windows)端修改，再回传到Server(Android)端，修改方面也不方便

   这不符合“挂载”的需求，于是想到是否能放弃FTP这个解决方案，转而使用别的解决方案。

　**修改解决方案—使用Samba服务**

- 由于具有上述的问题，通过查阅资料，发现解决这些问题的一些方案
  - 【Unix Like机器之间】在 Unix Like 的机器上，互相之间可以使用NFS(NetworkFile System)，在Client端挂载Server端提供的分享目录，即可在Client直接取用分享目录上的档案数据，该分享目录就像Client上的分区一般取用方便。
  - 【Windows机器之间】在Windows的机器上，则使用CIFS(CommonInternet File System)，即网上邻居，来分享别人提供的档案数据。
  - 【Unix Like机器与Windows机器之间】可以使用Samba服务，实现让Unix Like系统与Windows操作系统的SMB/CIFS(Server Message Block/Common Internet File System)网络协议进行链接。
- 使用Samba服务，在Android上搭建Samba服务器，局域网内在Windows连接服务器，可以解决上述提到的两个问题
  - Samba服务支持设置多个共享目录
  - Samba服务能够在Windows十分方便的对Android端文件取用，真正意义上实现“挂载”。

　**已完成的工作**

1. 嵌入式Samba服务需要浏览[Samba官网](https://www.samba.org/)，由于原理较为复杂，还没嵌入成功，后续需要继续进行文档的阅读与API的查阅。

2. 在浏览期间，发现Alfresco这家公司提供了[JLAN](https://www.alfresco.com/news/press-releases/alfresco-makes-leading-java-implementation-jlan-shared-file-drive-interface)，它提供了Microsoft Windows的CIFS（通用Internet文件系统）协议的Java客户端和服务器实现，允许我们可以轻松的嵌入各种系统中。后续可能从中切入，使用JLAN进行开发。

3. 在寻找同款产品的过程中，发现国内市面上在手机部署Samba服务器的APP可以说完全不存在，而国外只有一款APP--`LAN drive`，持续更新且安装后能正常使用，然而它必须在Google市场进行下载，且没有汉化。后续会使用该APP进行基本界面、功能的模仿设计。

4. `LAN drive`的使用较为流畅，验证确实可以解决上述发现的问题

   |                    使用了多个目录                    |               可以方便的添加共享文件夹               |                    方便的取用                    |
   | :--------------------------------------------------: | :--------------------------------------------------: | :----------------------------------------------: |
   | <img src="README_images\LAN drive 使用多个目录.png"> | <img src="README_images\LAN drive 添加多个目录.png"> | <img src="README_images\LAN drive 方便取用.png"> |
   |       打开共享根目录目录即为多个共享文件夹目录       |             可以方便的添加多个共享文件夹             |       可以使用Windows端的各个软件打开取用        |

**后续的工作**

1. 由于对于是否能够完成Samba服务的嵌入开发还未知，于是没有修改[项目描述](#项目描述)，后续尽量浏览理解文档，看看开发进度再决定是否修改整体解决方案。
2. 如果最终无法完成Samba服务的嵌入开发，则继续使用FTP进行需求解决。

## 项目描述

#### 1. 设计背景

- 随着智能手机的发展，手机移动端在人们生活中显得日渐重要。而出于娱乐、办公等方面的需要，手机端文件与PC端文件互相传输交流也成为了刚需。

- 对于手机访问PC端的共享文件，市面上已有较为成熟的解决方案（以Android手机为例）

  PC端设置文件共享+Android端安装`ES文件浏览器`

- 而PC端访问手机文件时，市面上也有一些解决方案，如MIUI自带的基于FTP的远程管理、ES文件浏览器的远程管理等。这些方案都有或多或少的弊端。

  |        解决方案        | <center>弊端</center>                                        |
  | :--------------------: | ------------------------------------------------------------ |
  |   MIUI自带的远程管理   | 1. 必须使用小米手机<br>2. 无法设置特定的文件夹进行共享<br>3. PC端需要手动输入FTP地址 |
  | ES文件浏览器的远程管理 | 1. APP集成度较高，功能众多，APP体量大<br>2. 无法使用用户名口令登录<br>3. 无法设置特定的文件夹进行共享<br>4. PC端需要手动输入FTP地址 |
  |    其他文件管理方案    | 需要手机端与PC端同时安装软件                                 |

#### 2. 设计目标（功能描述）

　　项目力求将手机端（Android系统）中的文件系统扩展作为PC端（Windows系统）的虚拟硬盘。

1. 设计解决方案，实现下述需求

    1. 在连接到同一个局域网时，手机端的文件系统自动显示在PC端文件资源管理器中，并作为一个网络位置（虚拟硬盘）可以方便的对文件进行增删改查。

    2. 另外提升用户体验，在后台运行手机端的应用与PC端的程序而无需用户介入，在连接同一个局域网后自动适配。
2. 基本思路
   1. 设计一款APP（AndroidDisk），实现当安卓手机与PC连在同一个局域网时，PC端能够便捷的使用windows自带的文件资源管理器管理安卓手机中的共享文件。
   2. 设计PC端的小程序，不断扫描所在局域网FTP端口，发现相应设备并确认身份后自动添加网络位置（虚拟网盘）。

1. 基本界面

   |                        Android端                         |                           PC端                            |
   | :------------------------------------------------------: | :-------------------------------------------------------: |
   | <img src="README_images/基本界面1.png" style="zoom:10%"> | <img src="README_images/基本界面2.png" style="zoom:200%"> |

2. 具体功能

   - 用户口令验证
   - 设置特定的共享文件
   - PC端扫描局域网，确认设备后自动添加

#### 3. 关键技术

##### 3.1 手机端app设计

1. APP实现在Android端搭建FTP服务器

   对比[apache ftp server](https://projects.apache.org/project.html?mina-ftpserver)与[swiftp](https://code.google.com/archive/p/swiftp/)的优劣再进行取舍

2. 设计相关UI交互实现登录口令设置与特定文件共享

##### 3.2 PC端小程序设计

- PC端使用talnet不断确认手机端口状态，并验证口令，添加网络位置
- 基本思路：确认端口状态 --> 验证口令 --> ftp连接 --> 添加网络位置到资源管理器

## 项目计划

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;预计18年11月正式开始毕设，本学期最后一周（1月17日前）完成。

| <center>时间段</center> | <center>计划内容</center>                                    |
| ----------------------- | ------------------------------------------------------------ |
| ~11月15日               | 查阅相关资料，比较两种方案，并选定其中一个进行毕设设计       |
| 11月15日~11月30日       | 写出基本的demo（实现FTP连接功能），并调试debug完成           |
| 11月30日~12月15日       | 进一步完善demo功能（口令登录、共享特定文件夹），并调试debug完成 |
| 12月15日~12月30日       | 进一步完善demo功能（口令登录、共享特定文件夹），并调试debug完成 |
| 12月30日~1月15日        | 设计PC端程序                                                 |


## 会议总结

#### 10/26 第一次会议

- 参考老师给定的第一个题目，`在手机中设定一个文件夹，当手机和电脑处于同一个局域网时，电脑中会出现一个文件夹`，查阅了相关资料，决定实现一个基于ad-hoc网络的文件共享系统
- 会议上导师的看法
  1. 手机需要root，并连接PC使用adb进行配置ad hoc网络模式，没有实际使用意义
  2. ad hoc网络研究热潮在十年前，由于运营商等方面的原因，认为其没有发展前景
- 于是放弃该想法，由于构想不成熟被毙，我根据构想找的论文也没有作用，所以决定于下次会议重新pre两篇论文

#### 10/30 第二次会议

- 由于家中有事，缺席第二次会议

