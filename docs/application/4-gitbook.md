# gitbook入门

## win10环境配置
### 1.下载node,在nodejs文件目录下，新建文件夹node_cache和node_global
> https://www.jianshu.com/p/6fbbf4c49040
### 2.设置环境变量
### 3.设置全局路径，在cmd中输入：(根据自己的文件输入相应的路径)
npm config set prefix "D:\application\node\node_global"
npm config set cache "D:\application\node\node_cache"
### 4.安装模块
npm install express -g
npm install -g express-generator
### 5.安装gitbook
npm install gitbook-cli -g


## mac环境配置
环境配置需要安装nodejs、gitbook
##### 安装 Node.js
Node.js 下载地址：https://nodejs.org/zh-cn/
##### 安装 GitBook
使用 NPM 安装 GitBook 的命令行工具：
```
$ sudo npm install gitbook-cli -g
```
##### 安装完成后查看 GitBook 的版本：
```
$ gitbook --version
```
##### 更新 GitBook命令：
```
$ npm update gitbook-cli -g
```
##### 卸载 GitBook 命令：
```
$ sudo npm uninstall gitbook-cli -g
```
##### 注意：
本例中使用的是全局安装 GitBook 命令，还有一种安装方式是本地安装，两种方式有什么不同呢？
**本地安装：**安装包会被下载到当前所在目录，因此只能在当前目录下使用。
**全局安装：**安装包会被下载到到特定的系统目录下，安装包能够在所有目录下使用。
##### 查看 macOS 下全局安装被安装到了哪里？
```
$ which gitbook
```
GitBook 被安装到了/usr/local/bin/gitbook,可以全局使用

# gitbook操作入门
##### 创建itheima文件夹，并切换到这个文件夹下面
```
$ mkdir itheima
$ cd GitBook/Library/Import/itheima/
```
##### 初始化gitbook工作目录，创建必要的文件夹
```
$ gitbook init
```
**README.md** - 项目的介绍都写在这个文件里
**SUMMARY.md** - GitBook 的目录结构在这里配置
##### 定义目录结构
**方法1：**先定义好目录结构，通过 gitbook init 自动生成目录结构对应的文件夹和 Markdown 文件
**方法2：**先创建好文件夹和 Markdown 文件再来编辑目录结构
##### SUMMARY.md
##### SUMMARY.md 的目录结构
```
# Summary

* [Introduction](README.md)
* [1.flask入门](docs/1.flask.md)
  * [1.1.环境搭建](docs/1.1.virtualenv.md)
* [2.项目实战](docs/2.project.md)
* [3.redis](docs/3.redis.md)
  * [3.1.redis](docs/3.1.redis.md)
* [4.mysql](docs/4.mysql.md)
  * [4.1.mysql基本操作](docs/4.1.mysql.md)
* [5.gitbook](docs/5.gitbook.md)
* [6.StarUML](6staruml.md)
```
##### 这个目录建好以后在根目录下执行命令：
```
$ gitbook init
```
##### 那些没有的目录和文件都会被创建：
##### 注意： gitbook init 只支持生成两级目录
##### 启动服务
```
$ gitbook serve
```
**执行 gitbook serve命令后，会先编译书籍gitbook build，如果没有问题会打开一个 Web 服务器，默认监听 4000 端口。如果编译有问题，会抛出错误信息**
##### 查看效果
```
http://localhost:4000/
```

## git push 推送大文件失败的处理办法
**原因：**不小心把大文件件放到git目录里了，并且执行了commit操作，导致无法上传代码。之前尝试了GitHub上传文件不能超过100M的解决办法,但是仍然提交不成功。故重新找了方法，也很简单。

```
git config http.postBuffer 524288000
```
**办法：**如果把提交大文件的几次提交取消掉
- 先查看最近的提交

```
git reflog
```
- 撤销commit,回退到大文件之前的版本。reset完成commit命令的撤销，但是不对代码修改进行撤销，可以直接通过git commit 重新提交对本地代码的修改

```
git reset 版本号
```

- 重新提交并推送代码

注：参考博客：https://www.cnblogs.com/NewBigLiang/p/7015887.html


## 自动生成目录
安装
```
npm install -g gitbook-summary  
```
使用
```
book sm -i node_modules
```
##### 以上，谢谢！

