# python

该脚本将解释它将做出什么样的改变，并在安装开始之前提示你。一旦你安装了自制软件，在你的顶部插入自制软件目录PATH环境变量。您可以通过在~/.profile文件底部添加以下行来完成此操作

```
# export PATH=$PATH:/path/to/dir
export PATH=/usr/local/bin:/usr/local/sbin:$PATH
```


You need to add it to your ~/.profile or ~/.bashrc file.
```
export PATH=$PATH:/path/to/dir
```
Depending on what you're doing, you also may want to symlink to binaries:
```
cd /usr/bin
sudo ln -s /path/to/binary binary-name
```
Note that this will not automatically update your path for the remainder of the session. To do this, you should run:
```
source ~/.profile 
or
source ~/.bashrc
```
```
mkdir pipenv_spider_py3
cd pipenv_spider_py3/
pipenv install

(pipenv_spider_py3-wWd0254S) bash-3.2$ which python3
/Users/liupan/.virtualenvs/pipenv_spider_py3-wWd0254S/bin/python3

exit

which python3
/Library/Frameworks/Python.framework/Versions/3.6/bin/python3
```
