首先,确保你的虚拟机启动了
在pycharm的菜单栏依次找到:Tools > Deployment > Configuration
![](/uploads/201905/analyst/attach_159f28d48b0d15e1.png)

![](/uploads/201905/analyst/attach_159f28d77cf9cf22.png)

![](/uploads/201905/analyst/attach_159f28dac01e31ce.png)
这个配置完成了之后，其实是相当于配置了一个ftp工具可以连接到服务器上，从而可以查看和修改服务器上的文件。你可以通过Tools > Deplotment > Browse Remote Host来打开相应的RemoteHost面板，这个面板显示的就是服务器上的文件，显示的范围是你在Deployment中的Connection选项卡下配置的Root path路径下的文件及文件夹。
![](/uploads/201905/analyst/attach_159f28e264b44abc.png)
配置远程python解释器
首先，通过File > Settings,打开设置选项卡。
在设置选项卡里，点击"Project:项目名"这个按钮，在展开的小项里再点击Project Interpreter，右边就会变成Interpreter的配置页面。
![](/uploads/201905/analyst/attach_159f28e7156a1b19.png)
配好之后,在remotehost里可以download远程文件,然后在本地编辑,upload,运行
![](/uploads/201905/analyst/attach_159f28eacae806d8.png)
可以发现运行的文件是远程的虚拟机里面的,使用的解释器也是远程的虚拟机里面的

我们的代码现在一共有三个版本：一个是本地代码，一个是服务器上的代码，还有一个是RemoteHost面板中的代码(这个地方的代码如果直接编辑了，其实也可以提交到服务器，但是不能直接运行)
所以，如果通过上面的配置进行远程调试的话，我认为大致流程应该是下面这样：
1.	在RemoteHost面板中，选中想要修改的代码，然后右键点击Download from here将内容下载至本地(这个本地是你在配置Deployment时设置的本地文件夹)。
2.	在本地(这个本地是你在配置Deployment时设置的本地文件夹)修改你的代码，修改完成后在编辑区域或者文件名上右键，选择upload to...来提交到服务器。
3.	在提交之后，你可以像普通调用本地解释器一样的直接运行本地的这个文件(但其实运行的是服务器的文件)
在这里，虽然RemoteHost里的文件可以直接编辑，但是并不建议这么做，因为这里编辑之后并不能直接运行。

![](/uploads/201905/analyst/attach_159f28f1f5a9cc1a.png)