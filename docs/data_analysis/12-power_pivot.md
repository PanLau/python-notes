# power bi

## 安装
1.先安装依赖
### http://go.microsoft.com/fwlink/?LinkID=179240
https://www.microsoft.com/zh-cn/download/confirmation.aspx?id=22
安装依赖报错
> https://www.zhihu.com/question/50117166
> https://www.pangzhan.net/181.html
下载工具
https://www.microsoft.com/zh-cn/software-download/windows10

dism.exe /online /enable-feature /featurename:NetFX3 /Source:D:\sources\sxs
dism.exe /online /enable-feature /featurename:NetFX3 /Source:C:\Users\liupan\Downloads\sxs 
```报错
C:\windows\system32>dism.exe /online /enable-feature /featurename:NetFX3 /Source:D:\sources\sxs


部署映像服务和管理工具
版本: 10.0.17763.1

映像版本: 10.0.17763.107

启用一个或多个功能
[==========================100.0%==========================]

错误: 0x800f0954

DISM 失败。不执行任何操作。
有关详细信息，请查看日志文件。

可以在 C:\windows\Logs\DISM\dism.log 上找到 DISM 日志文件
```


2.电脑重启
3.下载
> https://www.microsoft.com/zh-cn/download/details.aspx?id=7609
https://www.microsoft.com/zh-cn/download/confirmation.aspx?id=7609


一、先运行【检测windows环境安装与否.exe】，查看是否电脑底层的程序运行环境有缺少，缺少时安装一下即可，安装需要管理员权限，若是公司电脑没有管理员权限，请让IT部门协助安装完成。
.NetFrameWork4.62组件：（需win7sp1及以上才能安装）https://www.microsoft.com/zh-CN/download/details.aspx?id=53344
.NetFrameWork4.5组件：（需win7sp1及以上才能安装）：https://www.microsoft.com/zh-CN/download/details.aspx?id=42637
.NetFrameWork4.0组件：https://www.microsoft.com/zh-cn/download/details.aspx?id=17718

VSTO运行时：https://www.microsoft.com/zh-CN/download/details.aspx?id=48217

Microsoft® SQL Server® 2012 Native Client （有32位和64位区分，请根据OFFICE版本位数来安装不同位数的客户端）：https://www.microsoft.com/zh-CN/download/details.aspx?id=50402

二、运行【追加hosts映射( 管理员权限运行).exe】，网站路径的IP映射，让电脑可以对访问外部的IP地址101.132.130.88进行域名本地转换，即在hosts文件上添加一行 101.132.130.88 LiWeiJianWeb

三、运行【导入证书及设置EXCEL信任文件夹.exe】，让Excel对外部网络地址的程序信任，并且导入程序开发者的证书，让Excel信任日后存放在网络上的程序的版本更新，自动把新版本下载到本地使用。


安装完以上操作后，可在以下地址下载插件，如无意外下载完成后，双击即可安装成功，如有任何问题，欢迎及时反馈，QQ或微信号：190262897。
Excel催化剂插件下载
http://101.132.130.88/VSTO插件/Excel催化剂/setup.exe


C:\Program Files\Microsoft Office\Office16\ADDINS

C:\Users\liupan\AppData\Roaming\Microsoft\AddIns