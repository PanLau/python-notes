# 网络udp

## 1.ip地址
    192.168.1.0-->标记网络号
    192.168.1.255-->广播地址
    
    单播：点对点
    多播：多点
    单播是向一台电脑通信，多播是向一组电脑通信，广播是向局域网内所有的电脑通信
    
    python@ubuntu:~$ ifconfig
    inet 地址-->ipv4地址
    inet6 地址-->ipv6地址

    python@ubuntu:~$ ping ip地址 —>测试是否能进行通讯
    
    python@ubuntu:~$ ifconfig ens33 192.168.1.108
    sudo ifconfig ens33 192.168.1.108

## 2.端口
    ip:192.168.1.1-->标记一个电脑
    port:7788-->标记一个程序
    
    ps
    ps -aux
    kill pid
    pid是操作系统用来管理程序的值

## 3.socket
    1.创建套接字
    2.使用套接字进行收发数据
    3.用close关闭

## 4.如果需要直接运行一个Python程序，那么直接在终端中 python3 xxx.py
    如果想要练习或者验证某个知识点，那么可以用ipython3开启交互模式 ipython3

## 5.安装网络调试助手
    让Mac和ubuntu能互相通信
    Ubuntu设置为桥接模式
    sudo dhclient重新自动获取i地址
    关闭防火墙
   
    打开网络调试助手
    协议类型UDP
    本地IP地址
    本地端口
    目标IP地址
    目标端口

## 6.udp网络程序-发送、接收数据
    除了Windows（gbk）外，其他都是utf-8
    编码encode，解码decode

- 如果一个程序仅仅是发送数据
    1.创建套接字
    2.发送数据
    3.关闭

- 如果一个程序向用来接收数据
    1.创建套接字
    2.绑定
    3.接收
    4.关闭

## 流程：
先理课件0.5h，疑点讨论/打开视频，语速加快
敲课上代码
写作业

## 4. udp一般应用在哪些方面？
    一般用于多点通信和实时的数据业务。
    语音广播，视频，QQ，TFRP(简单文件传送)
    SNMP(简单网络管理协议)，RIP(路由信息协议)，DNS(域名解析)

## 3.udp广播是什么意思？
    打比方服务器地址192.168.35.13UDP广播地址要写成192.168.35.255服务器给处于同一个局域网中的每台电脑发送数据

UDP是一种面向无连接的协议，每个数据报都是一个独立的信息，包括完整的源地址或目的地址，它在网络上以任何可能的路径传往目的地，因此能否到达目的地，到达目的地的时间以及内容的正确性都是不能被保证的。

## 17. 接收方可否不绑定端口？为什么？
    1>不可以，
    2>udp通信的端口号每次都是会变化的，如果需要做成一个服务器的程序的话，是需要绑定的。就像国内公共号码，服务型的程序都是需要绑定的。