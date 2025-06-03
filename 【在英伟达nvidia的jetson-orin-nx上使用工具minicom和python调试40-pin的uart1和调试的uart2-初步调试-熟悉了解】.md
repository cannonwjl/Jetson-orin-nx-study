@[TOC](【在英伟达nvidia的jetson-orin-nx上使用工具minicom和python调试uart1和uart2-初步调试-熟悉了解】)

# 1、概述
近期开始学习英伟达的工控板，作为新手还是有很多不同懂得地方，但是一部分它用的linux系统，有点像玩树莓派，其中树莓派的40-pin和英伟达jetson的40-pin很像，通过自己一段时间学习调试串口，终于可以简单通信了。

  开始的时候，自己也是什么都不是很懂，从新手一点点摸索过来，现在使用工控机，调试串口简单通信也已经没有问题了，算是有些进步吧。
# 2、实验环境
初次了解，这里使用提供软硬件信息吧。
硬件信息：Jetson Orin nx
软件版本：5.10.104-tegra #1 SMP PREEMPT Sun Mar 19 07:55:28 PDT 2023 aarch64 aarch64 aarch64 GNU/Linux
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/5cdfe66a9d6513c0d53f885325a68c50.png)
其它硬件：usb串口转换器或者TTL转换模块

# 3、先行了解知识
 调试uart功能需要对jetson有些基础了解，也是看了一段时间资料才有个基本认识。
  （1）需要对硬件有所了解，知道硬件IO是在哪里,哪些怎么接。
  （2）安装相应的库或者软件，使用apt-get命令
  （3）给权限，配置权限，使其可以运行
  （4）相关资料查找，一般要去英伟达官网去了解
  如下是核心板外设硬件图
  ![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7098f813730b32728b1874c053a6b2d6.png)
   （5）uart2是调试引脚，不同配置，自动连接就可以使用。

   文件名称：Jetson Orin NX Series and Jetson Orin Nano Series（Product Design Guide）
   ![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/61bf538e09583eba5bdf1f804bfdfc16.png)

# 4、参考资料
自己学习也是参考了以下链接，需要罗列出来。
（1）英伟达官网链接，想要了解资料的话，一般都要去官网查相关资料的。
参考链接：[https://developer.nvidia.com/embedded/downloads](https://developer.nvidia.com/embedded/downloads)
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d857db5f7bf744400a73313d50b9858e.png)

（2）python脚本的程序代码编写参考博文，自己是根据这篇编写代码，同时了解每条python代码意义
参考连接：[https://blog.csdn.net/weixin_44350337/article/details/111623475](https://blog.csdn.net/weixin_44350337/article/details/111623475)
（3）如下链接，博主主要是提供了一个硬件参考图，告诉你哪个是哪个，从原理图让我们知道硬件设备。
参考链接：[https://blog.csdn.net/liuyang_xyz/article/details/126500097](https://blog.csdn.net/liuyang_xyz/article/details/126500097)

为此，寻找了原网站看了一下，我这个是orin nx的，所以选择对应的。

网站链接：[https://jetsonhacks.com/nvidia-jetson-orin-nano-gpio-header-pinout/](https://jetsonhacks.com/nvidia-jetson-orin-nano-gpio-header-pinout/)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0cbc37872eb18b43b5f17a1403c411e8.png)

为此，找到这个俯视图图片文件，如下，可以去条目一中英伟达文档哪里搜索。

文件名称：搜索：**Jetson Orin Nano Developer Kit Carrier Board**


![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/31625cc5c41adf5c6f63833d6c3ecd57.png)


（4）如下链接，主要是解决库问题，如果使用python的话，需要安装的库。
参考链接：[https://blog.csdn.net/qq_25662827/article/details/119381935](https://blog.csdn.net/qq_25662827/article/details/119381935)

```cpp
sudo pip3 install pyserial
sudo pip install pyserial
```

# 5-1、操作步骤-python脚本的方式（uart1）
### 1、硬件连接-引脚连接
需要查找硬件引脚，通过之前查找资料，我们知道uart1在40-pin上，uart2为调试端口。

如下，为我自己板子实物图，也就是J14和J12 ,上边有丝印

```cpp
//uart1 - j12
6号引脚-GND
8号引脚-uart1_TX
10号引脚-uart1_RX

//uart2-debug - j14
UART TXD  
UART RXD
GND

```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/628371b80080b984b40f092b1e182a32.png)

### 2、安装对应库
目前看来需要安装python对应的库，以下是必要的执行步骤
```cpp
sudo apt-get install python3-pip
sudo apt-get install python-pip
sudo pip3 install pyserial
```
其它尝试，以下非必须选项，在尝试过程中，自己尝试的输入其它命令

```cpp
sudo apt-get install jetson-io
sudo apt-get update
sudo /opt/nvidia/jetson-io/jetson-io.py
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fddb2f90059b5de28d8f466c429ad2c4.png)

### 3、代码编写
这里我在桌面新建个一个文件件，然后新建了一个文件，你需要自己找个合适位置，创建一个文件。

```cpp
//使用vim创建文件
sudo vim py_uart1.py

```
然后就是代码部分，输入代码如下（python）



```python
import serial as ser
import time
a = "12345"
b = 678
c = " I'm is xxxx.."


se = ser.Serial("/dev/ttyTHS0",9600)
while True:
    se.write(a.encode())
    se.write(str(b).encode("GB2312"))
    se.write(c.encode("GB2312"))
    se.write("\r\n".encode())
    print(se.readline().decode("GB2312"))
    time.sleep(1)
    print("send ok!")
    print(se.is_open)
            

```


### 4、文件设备给权限
需要给在/dev/ttyTHS0以777的运行权限。

```cpp
sudo chmod 777 /dev/ttyTHS0
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3490a5e0b070748a39f8b3c89c578cca.png)
### 5、运行测试
最后运行测试如下，我将py的文件和命令行的均放在一起，方便查看
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/548de67374ca110ea04a75fab1b9c0e8.png)
另一端是我电脑PC机接收到的
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/bcde3e36ba75400e5c1fdf9e0d247401.png)



# 5-2、操作步骤-minicom工具调试方式（uart1）

### 1、安装对应软件
调试的方法不只是一种，使用python这种，其实方便后续调试，编写脚本啥的，后来了解到使用minicom就不用这样费事，直接下载，然后使用就可以了，如下步骤就是如此。

```cpp
sudo apt-get install mincom
sudo minicom -D /dev/ttyTHS0
```
网上相关教学还是挺多的，我这里直接给出参照链接：
参照链接：[https://blog.csdn.net/Naisu_kun/article/details/117704009](https://blog.csdn.net/Naisu_kun/article/details/117704009)
### 2、运行调试
Linu终端如下
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/fd0a3dad77d903068b8296d05e93585d.png)
PC电脑接收端
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2df11516c5b6f87e569829079c277263.png)


# 5-3、操作步骤-调试uart2(调试端口)（uart2）
### 1、硬件连接-引脚连接
引脚之前已经确认了，不过还是要说明下，uart2是调试口，之前尝试时，不知道，然后用python脚本控制它，脚本内波特率是9600，发现不好使，后来发现115200就是可以的，所以调试口都不需要配置，直接可以用，主要是注意硬件连接。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/62cb5524a3dfa7360b1757df3b15b8ad.png)

串口硬件连接，使用的是TTL转USB模块（ps:之前调试lora那个模块，复用下<.>）
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/084ffd5ea5337c59c579a68af55ba798.png)

### 2、软件MobaXterm的下载
使用串口软件就不很方便了，推荐工具MobaXterm，并且有免费的请自行下载吧。

链接：https://mobaxterm.mobatek.net/download.html

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/116185d3d2850db4c54ecc96c691bac1.png)
使用方式如下
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/78713ab0a6784453b03a45b652be1332.png)

### 3、调试
连接好硬件后，重开机会有信息打印。
然后输入用户名和密码

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f151139aa371c5ad6a25b96aaab9abe7.png)

# 6、细节部分
### （1）No module named 'serial' 问题
相关连接：[https://blog.csdn.net/jiangchao3392/article/details/75107330](https://blog.csdn.net/jiangchao3392/article/details/75107330)
此问题一般就是没有==安装库==导致的

### （2）pip3: command not found
运行pip3之前，先要安装pip3,顺序要是反了就会报。

### （3）py文件报告，无法运行
我自己是手打的一遍代码，所以报了很多语法错误，代码敲错得原因。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ae656fdb0957207b745322fc79837ce7.png)

### （4）收到数据会出现乱码
一般要检查硬件连接和GND，还有是波特率，另外使用minicom时，默认波特率是115200
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b87f1b06b8c16bb086e45d8009860332.png)
（5）uart2是调试，默认115200
在尝试uart2时，发现9600波特率就是不行，后来才知道是调试口，如下使用python文件控制
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7675dd3c9a1a5a1d831b3bdc1d2ee06f.png)


# 7、总结
细节满满啊，不过从最初一点不同，到现在，已经明白许多了，总是有些收获的。


 

