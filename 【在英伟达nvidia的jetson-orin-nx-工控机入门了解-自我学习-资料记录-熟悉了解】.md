@[TOC](【在英伟达nvidia的jetson-orin-nx-工控机入门了解-自我学习-资料记录-熟悉了解】)

# 1、概述
近期开始学习英伟达的工控板，作为新手还是有很多不同懂得地方，但是一部分它用的linux系统，有点像玩树莓派，自己也看了一些资料，最近也是调试了几个外部接口，想着将一些资料整理下。

  一般用这种工控机，都是跑Ai算法什么的，我还没到那一步，至少现在还没到，一直调试底层的驱动来着，有很多资料需要了解。
# 2、实验环境
使用提供软硬件信息。
硬件信息：Jetson Orin nx
软件版本：5.10.104-tegra #1 SMP PREEMPT Sun Mar 19 07:55:28 PDT 2023 aarch64 aarch64 aarch64 GNU/Linux
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5cdfe66a9d6513c0d53f885325a68c50.png)
本章只是一些资料记录，不东西。

# 3-1初次接触工控机版本-真的好多-新手一脸懵逼
最开始接触时，发现英伟达的工控机版本好多啊，不同型号之间，各种尾椎，根本不知道有什么差别。可谓是”**真的好多-新手一脸懵逼“**。
后来查资料时，看官方文档，发现了一种区分不同版本方式，可以方便帮我们区分，就在下载资料文档里。
连接文档：[https://developer.nvidia.com/embedded/downloads](https://developer.nvidia.com/embedded/downloads)

==同是这个文档也是下载资料的地方，所有英伟达资料都可以来这里翻一翻。==

如下图:
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b622708491ce732645a520b7f63f9c58.png)

如图所示，可以说，目前英伟达的工控机就这样些，都是jetson开头。

越往上价格越贵，性能也就越高，越往下，价格也就越便宜，但是性能就会差些。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/50cd2a4a3c31ed50ce25dded3d9b4b49.png)

目前网上jetson AGX orin,一套（载板+核心板）基本都过万了。
我目前买的jetson orin nx，是核心板是原版，加上国产载板，大概8K左右。

# 3-2 啥是载板和核心板
记得当初自己确实不知道，以为工控机就是一个整体，其实是由两部分组成的。如果你只买核心板，或者载板，其实是跑不起来的，在一些二手交易平台上，有些买的很便宜的，可能就是只有一部分。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/05310cd71f23fe9dc33206640e9cb3f2.png)

# 3-3 查看资源资料
 如下图，有些文档，会说明，不同核心板之间，资源有什么不同，这种对比表。
 
如下是链接论坛：[https://forums.developer.nvidia.com/c/agx-autonomous-machines/jetson-embedded-systems/70](https://forums.developer.nvidia.com/c/agx-autonomous-machines/jetson-embedded-systems/70)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4a85ecdc8808fa86e5f439e8af5834b2.png)


参考文档：[https://docs.nvidia.com/jetson/archives/r34.1/DeveloperGuide/index.html#](https://docs.nvidia.com/jetson/archives/r34.1/DeveloperGuide/index.html#)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fa9d6ded149472e3d0d210491146e308.png)

# 3-4 了解外设资源

如下图，有些文档，会标明这个核心板都有什么资源module，具体可以去搜搜。

文件名称：Jetson Orin NX Series and Jetson Orin Nano Series
（）Product Design Guide）

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b8e766a36da8a772ff00f14ab653f476.png)
# 3-5 查看博客论坛
对于一些问题，自己不知道时候，可以看看论坛里是否有说明的，也许有解决方式，只不过是英文的。

链接：https://forums.developer.nvidia.com/

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/af86a9adf83ab4ed3277560907ff5448.png)
# 3-6 底层配置pinmux
一般要调试底层驱动的话，修改IO什么的就会涉及这个，应该是基本绕不开。
自己目前理解，它是一个IO表，可以配置IO相应状态，然后生成文件，比较底层了。再给系统，目前还没有到，不过感觉以后应该会用到的。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/589d9889c74c6e7d513658411d90b488.png)
如下表格。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/dfd0576871fc7ae0ba547e377542a3c9.png)
# 3-7 Linux驱动开发
 目前看来常见的驱动，其实Linux本身已经带有了，基本不需要再次开发了，直接使用即可，如果是新的设备，驱动一般会从厂家要。
 如下图，如果做驱动相关开发，需要了解一下整体框架，正点原子这个图就很直观了。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/489ee4129668ae08565cdb3f70e2ad7b.png)



# 3-8 AI相关
我们使用这个工控板，一般不是说简单玩Linux，还是有跑些AI的东西，或者图像识别，这块还没看开始学习，自己的同学，有使用一个叫yolov5的工具，做图片或者视频识别的，更深些就没了解，暂时还没到那。

# 3-8 jetson-io和40-pin工具
如果调试40-pin的话，目前听说jetson-io这个工具还是挺好用的，自己也在不断尝试过程中。
链接：https://jetsonhacks.com/nvidia-jetson-xavier-nx-gpio-header-pinout/
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f2d8c757d6d4a5d07096657c7e7c3c5a.png)

# 4、总结
资料可以说还是比较多的，需要消化些，但对于细节部分，或者某个专业方向，可能会有些少，现总结这些吧。

