---
title: 解锁Socket网络编程
hidden: true
date: 2019-12-21 09:09:08
tags:
- 计算机网络
- Java
categories: 网络编程
---

解决问题：数据分片、消息粘包、消息丢包

性能优化：性能调试、性能分析、并发封装

相应拓展：内存技术、桥接技术

课程安排：

+ 底层协议  报文、协议、Mac地址、IP、端口、服务器等概念
+ UDP、TCP  通过讲解协议区别、性能比较、适用场景，从而推导出NIO
+ NIO大家族 Selector，Channel(FileChannel、ScoketChannel、ReadChannel)，Buffer(ByteBuffer、CharBuffer、IntBuffer)
+ 案例实战：群聊天室(多客户端链接、UDP扫描功能、聊天消息转发、向NIO的转型、心跳包的实现)，文件快传(基于流的传输、文件发送中断、消息分片技术、消息混发技术)，即时语音(直播推流、语音采集、语音压缩、接收与播放，实时消息模型)等



## Scoket网络编程快速入门

**什么是网络、计算机网络的构成是什么？**

&emsp;&emsp;在计算机领域中，网络是信息传输、接收、共享的虚拟平台；通过它把各个点、面、体的信息联系到一起，从而实现这些资源的共享；网络是人类发展史来最重要的发明，提高了科技和人类社会的发展。

**什么是网络编程？**

&emsp;&emsp;网络编程从大的方面说就是对信息的发送到接收；通过操作相应的API调度计算机硬件资源，并利用传输管道(网线)进行数据交换的过程。

基础层：物理层、数据链路层、网络层

传输层：TCP-UDP协议层、Socket

高级层：会话层、表示层、应用层

##### Socket(套接字)与TCP、UDP

###### What is Scoket?

<span style="color:red">个人对于Socket的理解就是对于TCP、UDP的一个封装。</span>

简单来说是IP地址与端口的结合协议（RFC 793）

一种地址与端口的结合描述协议

TCP/IP协议的相关API的总称；是网络API的集合实现

涵盖了：Stream Socket/Datagram Scoket

###### Scoket的作用和组成

Scoket=IP Address+Port number

在网络传输中用于唯一标示两个端口之间的链接

端口：包括（IP+Port）两个计算机中的进程要互相通信，不仅要知道对方的IP地址（为了找到对方的计算机），而且还要知道对方的端口号（为了找到对方计算机中的应用进程）。<http://localhost:8080/user>

localhost：IP地址

8080：端口号

4个要素：客户端地址、客户端端口、服务地址、服务器端口

物理地址因为不是统一的，全世界存在着各式各样的网络，它们使用不同形式的物理地址。而统一的IP地址，通过ARP和RARP协议进行物理地址和IP地址之间的转换，然后再进行IP地址之间的通信。从而解决了不同形式物理地址之间通信需要进行的复杂的物理地址转换工作

###### Socket之TCP

1. TCP是面向连接的通信协议

通过三次握手建立连接，通讯完成时要拆除连接。

由于TCP是面向连接的所以只能用于端到端的通讯，因为连接双方传输的报文段里面存放着维护逻辑连接的一些信息，所以不支持多播，广播；而UDP支持一对一、一对多、多对一、多对多的交互通信。

###### Socket之UDP

UDP是面向无连接的通信协议

UDP数据包括目的端口号和源端口号信息

由于通讯不需要连接，所以可以实现广播发送，并不局限于端到端



##### Client-Server Application

TCP/IP协议中，两个进程间通信的主要模式为CS模型

主要目的：协同网络中的计算机资源、服务模式、进程间数据共享

常见的：FTP、SMTP、HTTP

#### Socket TCP牛刀小试

构建TCP客户端、服务端

客户端发送数据

```java
import java.io.*;
import java.net.Inet4Address;
import java.net.InetSocketAddress;
import java.net.Socket;

/**
 * @author Hogan_Lee
 * @create 2019-12-21 11:11
 */
public class Client {
    //构建一个客户端入口
    public static void main(String[] args) throws IOException {
        //初始化一个Socket
        Socket socket=new Socket();
        //超时时间
        socket.setSoTimeout(3000);
        //设置连接的目的进程的主机IP和进程端口号
        //因为连接的是本机上的另外一个进程，所以IP地址是本机上的另外一个地址
        //额，这个超时时间的作用是什么？
        socket.connect(new InetSocketAddress(Inet4Address.getLocalHost(),2000),3000);
        //System.out.println("已发起服务器连接，并进入后续流程~");
        //System.out.println("客户端信息："+socket.getLocalAddress()+"P:"+socket.getLocalPort());
        /**
         * getInetAddress:Returns the address to which the socket is connected.
         * getPort:Returns the remote port number to which this socket is connected.
         */
        //System.out.println("服务端信息："+socket.getInetAddress()+"P:"+socket.getPort());
        try {
            //发送接收数据
            todo(socket);
        }catch (Exception e){
            System.out.println("异常关闭");
        }

        //释放资源
        socket.close();
        //System.out.println("客户端已退出");

    }

    public static void todo(Socket client) throws IOException{
        //构建键盘输入流
        InputStream in=System.in;
        BufferedReader input=new BufferedReader(new InputStreamReader(in));

        //得到Socket输出流，并转换为打印流  向流写数据
        OutputStream outputStream=client.getOutputStream();
        PrintStream socketPrintStream=new PrintStream(outputStream);

        //接收服务器端的返回
        //得到Socket输入流，并转换为BufferedReader  从流中读取数据
        InputStream inputStream=client.getInputStream();
        BufferedReader socketBufferedReader=new BufferedReader(new InputStreamReader(inputStream));


        boolean flag=true;
        do {
            //键盘读取一行
            String str = input.readLine();
            //发送到服务器
            socketPrintStream.println(str);

            String echo = socketBufferedReader.readLine();
            if ("bye".equalsIgnoreCase(echo)) {
                flag=false;
            }else {
                System.out.println(echo);
            }
        }while (flag==true);

        //资源释放
        socketBufferedReader.close();
        socketPrintStream.close();
    }
}
```

服务端读取数据并打印

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public Server() {
    }

    public static void main(String[] args) throws IOException {
        ServerSocket server = new ServerSocket(2000);
        System.out.println("服务器准备就绪~");
        System.out.println("服务端信息：" + server.getInetAddress() + "P:" + server.getLocalPort());

        while(true) {
            Socket client = server.accept();
            Server.ClientHandler clientHandler = new Server.ClientHandler(client);
            clientHandler.start();
        }
    }

    private static class ClientHandler extends Thread {
        private Socket socket;
        private boolean flag = true;

        public ClientHandler(Socket socket) {
            this.socket = socket;
        }

        public void run() {
            super.run();
            System.out.println("新客户端连接：" + this.socket.getInetAddress() + "P:" + this.socket.getPort());

            try {
                PrintStream socketOutput = new PrintStream(this.socket.getOutputStream());
                BufferedReader socketinput = new BufferedReader(new InputStreamReader(this.socket.getInputStream()));

                do {
                    String str = socketinput.readLine();
                    if ("bye".equalsIgnoreCase(str)) {
                        this.flag = false;
                        socketOutput.println("bye");
                    } else {
                        System.out.println(str);
                        socketOutput.println("回送:" + str.length());
                    }
                } while(this.flag);

                socketinput.close();
                socketOutput.close();
            } catch (Exception var12) {
                System.out.println("连接异常断开~");
            } finally {
                try {
                    this.socket.close();
                } catch (IOException var11) {
                    var11.printStackTrace();
                }

            }

            System.out.println("客户端已退出:" + this.socket.getInetAddress() + "P:" + this.socket.getPort());
        }
    }
}
```



###### 报文、协议、Mac地址

+ 报文段

  报文段是指TCP/IP协议网络路由网段、IP地址、交换协议等IP数据包

  用以查询各个网络路由网段、IP地址、交换协议等IP数据包

  报文段充当整个TCP/IP协议数据包的导航路由功能

  报文在传输过程中会不断地封装成分组、包、帧来传输

  封装方式就是添加一些控制信息组成的首部，即报文头

+ 传输协议

  协议顾名思义，一种规定，约束

  约定大于配置，在网络传输中依然适用；网络的传输流程是健壮的，稳定的，得益于基础的协议构成

  简单来说：A——>B的传输数据，B能识别，反之B->A的传输数据A也能识别，这就是协议。

+ Mac地址

  Media Access Controller或者Medium Access Controller

  意译为媒体访问控制，或称为物理地址、硬件地址

  用来定义网络设备的位置

  形如：44-45-53-54-00-00；与身份证类似

###### Ip、端口、远程服务器

互联网协议地址（英语：Internet Protocol Address，又译为网际协议地址），缩写为IP地址（英语：IP Address）

是分配给网络上使用网际协议（英语：Internet Protocol，IP）的设备的数字标签

常见的Ip地址分为IPv4与IPv6两大类

+ IPV4

IP地址由32位二进制数组成，常以xxx.xxx.xxx.xxx形式表现，每组xxx代表小于或等于255的十进制数

如：208.80.152.2

分为A、B、C、D、E五大类，其中E类属于特殊保留地址

总数量：4294967296个（即232）：42亿个；最终于2022年2月3日用尽

如果主机号是全1，那么这个地址为直接广播地址

Ip地址“255.255.255.255”为受限广播地址

+ IpV6

总共有128位长，IPv6地址的表达形式，一般采用32个十六进制数。也可以想象为1632个

由两个逻辑部分组成：一个64位的网络前缀和一个64位的主机地址，主机地址通常根据物理地址自动生成，叫做EUI-64（或者64-位扩展唯一标识）

2001：0db8:85a3:0000:1319:8a2e:0307:7344

IPv4转换成IPv6一定可行，IPv6转换成IPv4不一定可行

**端口**

如果把IP地址比作一个快递站，端口就是不同的收件人

派件业务——>分用

寄件业务——>复用

0到1023号端口以及1024到49151号端口都是<u>特殊端口</u>

计算机之间依照互联网传输层TCP/IP协议的协议通信，不同的协议都对应不同的端口

49152到65535号端口属于“动态端口”范围，没有端口可以被正式的注册占用



问题：端口总数数：65536，连接能建立多少个？65536个？

有的电脑可以有n个Ip，n*65535个连接

![数据传输层次](http://cdn1.hikariblog.cn/%E6%95%B0%E6%8D%AE%E4%BC%A0%E8%BE%93%E5%B1%82%E6%AC%A1.PNG)



**远程服务器**

局域网：一般而言，家里的环境以及公司相互电脑之间的环境都属于局域网

我和你们的电脑之间属于互联网，而非局域网

默认的：我的电脑无法直接链接到你们的电脑

![](http://cdn1.hikariblog.cn/%E8%BF%9E%E6%8E%A5%E7%A4%BA%E6%84%8F%E5%9B%BE1.PNG)

通过一个因特网上的IP地址的服务器才能连接到，不同局域网主机之间不能直接连接，如果可以地址重复了怎么办，地址也不够用呀

![](http://cdn1.hikariblog.cn/%E8%BF%9E%E6%8E%A5%E7%A4%BA%E6%84%8F%E5%9B%BE2.PNG)

HTTP/HTTPS都是使用的TCP



# Socket-UDP快速入门

英语：User Datagram protocol，缩写为UDP

一种**用户数据报协议**，又称**用户数据报文协议**

是一个简单的面向数据报的传输层协议，正式规范为RFC 768

用户数据协议、非连接协议

### 为什么不可靠？

它一旦把应用程序发给网络层的数据发送出去，就不保留数据备份

UDP在IP数据报的头部仅仅加入了复用和数据校验（字段）

发送端生产数据，接收端从网络中抓取数据。

结构简单、无校验、速度快、容易丢包、可广播

### UDP能做什么

DNS、TFTP、SNMP

视频、音频、普通数据（无关紧要数据）

UDP的报文有两个字段：数据字段和首部字段。首部字段很简单，只有8个字节，由四个字段组成，每个字段都是两个字节。各个字段的意义如下：

- 源端口  源端口号
- 目的端口  目的端口号
- 长度  UDP用户数据报的长度
- 检验和  差错检测码，防止UDP用户数据报在传输的时候出错

###### UDP包最大长度

16位->2字节 存储长度信息(单位：字节)

2<sup>16</sup>-1=65536-1=65535

自身协议占用：32+32位=64位=8字节

65535-8=65507 byte



###### UDP核心API讲解

**API-DatagramSocket**

UDP并没有像TCP的客户端`Socket`和服务端`ServerSocket`一样的概念，`DatagramSocket`类既可以发送也可以接收。

额！TCP建立的逻辑链接不是全双工的吗？

1. 用于接收和发送UDP的类
2. 负责发送某一个UDP包，或者接收UDP包
3. 不同于TCP，UDP并没有合并到Socket API中

`DatagramSocket()` 创建简单实例，不指定端口和IP.

`DatagramSocket(int port)` 创建**监听**固定端口的实例

`DatagramSocket(int port,InetAddress localAddr)` 创建固定端口指定IP的实例

`receive(DatagramPacket d)`:接收

