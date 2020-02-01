
## anaconda 的使用
### 创建虚拟环境
```
conda create -n data_web python=3.6
```
### 4、使用激活(或切换不同python版本)的虚拟环境
activate data_web

pip install -r F:\Data_for_web\src\requirements.txt  -i https://pypi.douban.com/simple/ 


Created by:liupan
Created date:2019-1-22
Updated by:liupan


[TOC]
### 定时脚本项目1
	git 仓库地址 ：a（http://gitlab.xxxx.com/analysisdata/DataMachine_for_timer）

```
任务ID	任务描述	运行模式	Cron	负责人	状态	操作
148	xxx	GLUE(Shell)	00 50 2 * * ?	xxx	NORMAL	



```
### anaconda 在Windows下自启动
> https://www.cnblogs.com/zlslch/p/6984403.html

```
import inspect
import logging
import os
import win32serviceutil
from notebook.notebookapp import NotebookApp, JupyterApp

# 作为服务运行时的工作目录是system32，这里改为文件所在目录
current_file = os.path.abspath(inspect.getfile(inspect.currentframe()))
os.chdir(os.path.dirname(current_file))

class NotebookService(win32serviceutil.ServiceFramework):

    _svc_name_ = "JupyterNotebook"
    _svc_display_name_ = "Jupyter Notebook Service"
    _svc_description_ = "Jupyter的服务啦"

    def __init__(self, args):
        super().__init__(args)
        self.app = NotebookApp()

    def _init_notebook(self):
        JupyterApp.initialize(self.app)
        self.app.init_configurables()
        self.app.init_components()
        self.app.init_webapp()
        self.app.init_terminals()
        self.app.init_server_extensions()
        self.app.init_mime_overrides()
        self.app.init_shutdown_no_activity()

    def SvcDoRun(self):
        self.app.config_dir = "config" # 设置配置文件目录
        self._init_notebook()
        logging.getLogger("NotebookApp").addHandler(logging.FileHandler("notebook.log"))
        self.app.start()

    def SvcStop(self):
        self.app.stop()

    def SvcShutdown(self):
        self.SvcStop()


if __name__ == '__main__':
    win32serviceutil.HandleCommandLine(NotebookService)
```






