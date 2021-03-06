# 网络tcp

## 1.TCP的意义
udp不稳定，tcp稳定
UDP：无连接，不保证可靠，速度快，追求数据及时到达，而不要求正确顺序到达
TCP：有连接，保证可靠、按照一定顺序送达

CS:Client --> Server
BS:

## 2.推荐书籍：TCP/IP详解 卷1:协议

TCP->打电话，UDP->写信

## 3.TCP当客户端，网络调试助手协议类型TCP服务器
    1.创建套接字
    2.链接服务器
    3.发送/接收数据给服务器
    4.关闭套接字

区别：udp每次在发送数据的时候，都需要协商对方的ip和port，因为它不像那样有链接，因此必须都要写
    Tcp只要用connect链接一次，就确定了目的ip及port，所以在发送的时候，仅仅写上数据，不要写对方的ip port，操作系统在发送的时候会把connect链接的那个ip和port自动填写上

## 4.tcp服务器
    1.创建套接字—>有了一个电话机
    2.bind绑定信息—>插入了一个固定的手机号的卡
    3.listen将套接字变为被动套接字—>将手机设置为响铃模式
    4.accept等待客户端的链接，accept默认堵塞，accept有返回值（元祖）—>等待电话机响铃
    5.recv/send为客户端服务
    6.close关闭套接字

    当客户端下线的时候，服务器这里的表现是：
    1.recv解堵塞
    2.recv的返回值为空

## 5.注意点：先写注释，衡量标准注释占50%；vim xxx.py；金山打字通20分钟；分清主次，搞清做事的目的，不要本末倒置；思考问题的能力；与同桌讨论，与牛人聊天

with open 不管在这里是否产生异常，那么with这个语句，一定会保证调用f.close

## 9. 如果一个tcp客户端已经连接了服务器，那么服务器端如果关闭了listen时的套接字，那么服务器能否正常为这个已经连接的客户端服务？为什么
能，因为tcp连接后发送消息是给accept返回的新的socket，listen关闭并不会导致那个新的socket关闭

## 10. 关闭listen后的套接字和关闭accept返回的在套接字各有什么影响？
关闭listen后的套接字就不能再接受新的客户端的请求了；关闭accept返回的套接字，服务器就不能再接收之前连接中的客户端的消息了

## 11. 单工、半双工、全双工的区别和联系
1、单工
单工就是指A只能发信号，而B只能接收信号，通信是单向的，就象灯塔之于航船-灯塔发出光信号而航船只能接收信号以确保自己行驶在正确的航线上
2、半双工（Half Duplex）
半双工就是指A能发信号给B，B也能发信号给A，但这两个过程不能同时进行。最典型的例子就象我们在影视作品中看到的对讲机一样： 在这里，每方说完一句话后都要说个OVER，然后切换到接收状态，同时也告之对方-你可以发言了。如果双方同时处于收状态，或同时处于发状态，便不能正常通信了
3、全双工 （Full Duplex）
全双工比半双工又进了一步。在A给B发信号的同时，B也可以给A发信号。典型的例子就是打电话。

