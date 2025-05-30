@[TOC](【bash:xxx:command not found问题,在英伟达nvidia的jetson-orin-nx上遇到的>>>解决方式之一】)

# 1、概述
近期开始学习英伟达的工控板，作为新手还是有很多不同懂得地方，但是一部分它用的linux系统，有点像玩树莓派，虽然我树莓派也没玩得6，所以一直持有新手心态。

  当初买的jetson-orin-nx工控板，被别人借走用了一段时间，拿回来后出现问题，解了两天才解开，也算是自我学习一部分吧，将这块记录下来。
# 2、实验环境
初次了解，这里使用提供软硬件信息吧。
硬件信息：Jetson Orin nx
软件版本：5.10.104-tegra #1 SMP PREEMPT Sun Mar 19 07:55:28 PDT 2023 aarch64 aarch64 aarch64 GNU/Linux
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5cdfe66a9d6513c0d53f885325a68c50.png)

# 3、问题描述：bash:xxx:command not found问题
在拿回来的时候，尝试在终端敲打正常命令，如“ls查看目录，pwd，或者cd”，均报告bash:xxx:command not found。 
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/aea10fd749c072d9ab6047e226840492.png)

然后上网查解决方式大部分都是，输入一段命令，如下，简单配置环境变量。

```cpp
export PATH=/bin:/usr/bin:$PATH
```
但是这样其实指标不治本，一旦重启，或者新开一个终端就又不行了。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0b9c883e93b591e415182f8ea75b23c3.png)
# 4、我的努力
### 第一种方式：加入指令方式
如上问题描述，就是加入命令的方式，但这样其实并不长久，每次重启或者新开终端都要输入，很麻烦。
```cpp
export PATH=/bin:/usr/bin:$PATH
```
### 第二种方式：使用echo $PATH命令查看环境变量
通过使用命令

```cpp
echo $PATH
```
我发现，返回的环境变量是：/usr/local/cuda-11.4
这显然是不对，说明别人动过这个环境变量。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/19308b50e9f321b8bd64b0ccdde782af.png)
于是思路变成了，在哪里可以设置这个环境变量。

那么默认一般应该是什么环境变量呢，如下图，在另一台nx上，如下显示。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/838e5e156bf71e11704276a4ad2d7492.png)
如下将其辅助出来，这里指导的是大部情况，并且和板子也有些关心，使用jetson ,也就是英伟达相关工控机，并未查看其它板子。
```cpp
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

### 第三种方式：查看对比/etc/profile 文件内容
在网上有资料显示，通关修改这下便内容，或者加入环境变量内容，就可以增加环境变量，越是我找了另一个工控板上，对比了下内容，发现并没有区别，说明这块并没有更改过。具体内容如下，
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/70c364e0bfa4d75ca775a68c214ec76b.png)

### 第四种方式：改写/etc/bash.bashrc文件内容
后来按照网上说明，说要改~/.bashrc内部内容，自己一度认为是/etc/bash.bashrc下的这个目录下的文件，改写完成了如下图。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/afd72d64b03bac0f423aac1dceb01776.png)

在这个文件加入后内容后，发现

```cpp
文件
/etc/bash.bashrc
加入内容
export PATH=$PATH:/usr/local/bin
export PATH=$PATH:/usr/local/sbin
export PATH=$PATH:/usr/sbin
export PATH=$PATH:/usr/bin
export PATH=$PATH:sbin
export PATH=$PATH:bin
```
运行 source /etc/bash.bashrc，结果界面变成白色了，但是新开一个终端，还是不行，说明问题点不是在这。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/50074082d8d64badf3b22679cb764bfa.png)

说明问题点不再这里。

# 5、解决方式
后来想到=="~/.bashrc"==文件，可能指的不是“/etc/bash.bashrc”文件。以为~/.bashrc是泛指以“bashrc”结尾的文件，就是有个这样"~/.bashrc"文件。
于是，直接看这个文件，命令如下。

```c
cat ~/.bashrc
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4fcb56414aa77ca6e19c110ec11c74df.png)
可以看到就是上图，是加入的内容，才导致，其它不可用，注释掉就可以。

后来查看使用history命令，确实发现当初动过这个文件。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/82c15e0ed21fb8f8f3e40175e7060d6e.png)


# 6、解决思路
有时候就这这样，你遇到一个问题，解决了，最好不要就这样过去，要了解为啥能解决，了解深些。
我总结目前如果遇到这类问题，并且通过网上资料来看，一般有几种方式。
### 1、命令未安装
命令未安装：输入的命令没安装在系统上，则会出现这个错误提示。需要先安装该命令，然后才能在终端中使用它。说白了就没有这个命令，需要用apt-get，或者其他命令安装上。
### 2、环境变量配置不对

环境变量配置不对：命令所在的路径不在 PATH 环境变量中：输入的命令已经安装在系统上，但是终端无法找到该命令的路径，也会出现这个错误提示。需要将命令添加 PATH 环境变量中。目前来说，就如上边解决方式，加入到~/.bashrc文件中，当然看到网上还是有说其它文件，比如/etc/profile文件等

### 3、拼写错误
命令名拼写错误：如输入的命令名拼写错误，也会出现这个错误提示。需要检查输入的命令名是否拼写正确性，不过多尝试几个命令，都不行的话，一般就是本篇相似情况，整个环境变量被改动了。

# 7、细节部分
作为新手还是有很多不懂得地方，这些需要查找资料来补充。
（1）在Linux下，~/.bashrc 文件 和 bash.bashrc 文件，有什么区别 ，此问题询问了chatgpt，以供参考

```cpp
~/.bashrc 文件和/etc/bash.bashrc 文件都是 Bash shell 的配置文件，但它们的作用和范围有所不同。

~/.bashrc 文件是每个用户的个人 Bash shell 配置文件，通常位于用户的家目录下。该文件包含用户自定义的 shell 配置，例如别名、环境变量、命令别名等。当用户登录时，Bash shell 会自动加载该文件。

/etc/bash.bashrc 文件是全局 Bash shell 配置文件，适用于所有用户和所有 Bash shell。该文件包含系统管理员定义的全局 Bash shell 配置，例如全局别名、全局环境变量等。当用户登录时，Bash shell 会自动加载该文件。

因此，~/.bashrc 文件和/etc/bash.bashrc 文件的区别在于它们的作用范围。~/.bashrc 文件是每个用户的个人配置文件，/etc/bash.bashrc 文件是全局配置文件。

需要注意的是，不同的 Linux 发行版可能会有所不同。在某些系统上，可能没有 /etc/bash.bashrc 文件，或者该文件被命名为 bashrc 或其他名称。如果您有疑问，请参考您所使用的系统的文档。
```
（2）环境变量 有什么用，或者在Linux中起什么作用，干什么的，同样询问了chatgpt，以供参考

```cpp
环境变量是一组在操作系统中用来控制系统行为的变量。在 Linux 中，环境变量被广泛使用，它们可以控制系统的行为、设置用户的配置和提供有用的信息。

以下是环境变量在 Linux 中的主要用途：

控制系统行为：环境变量可以用来控制系统的行为。例如，PATH 环境变量指定了系统在哪些目录中查找可执行文件，LANG 环境变量指定了系统使用的语言环境。

设置用户的配置：环境变量可以用来设置用户的个人配置。例如，HOME 环境变量指定了当前用户的家目录，USER 环境变量指定了当前登录用户的用户名。

传递信息：环境变量可以用来传递信息。例如，在 Makefile 中，可以使用环境变量来传递编译器选项和链接器选项。

程序交互：某些程序使用环境变量来进行交互。例如，LESS 环境变量指定了 less 命令在显示文本时的选项。

在 Linux 中，环境变量可以使用 export 命令设置和修改，可以使用 echo 命令查看。通常，环境变量的名称使用大写字母表示，例如 PATH、HOME 等。

总之，环境变量是 Linux 中非常重要的概念，它们可以控制系统行为、设置用户配置、传递信息和支持程序交互。
```
（3）打开新的终端报错，因为一个符合打错了，这里“#”号代表注释，却打成了“$”.
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/da3cd7a6d90d832aa18fef0234432cfe.png)



# 8、总结
一点点积累吧，不积硅步，无以至千里吧。
