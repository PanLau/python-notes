# 定时任务
### 原因：由于历史脚本混乱，现在需要优化整合进行管理
### 流程：

1.从 gitlab 拉取最新代码，为避免文件夹冲突，
文件路径 E:\github\DataMachine

>首次需要 colne

```
$ git clone http://liupan010161@gitlab.xxxx.com/jixiang/DataMachine.git
Cloning into 'DataMachine'...
remote: Counting objects: 2989, done.
remote: Compressing objects: 100% (2149/2149), done.
remote: Total 2989 (delta 1035), reused 2653 (delta 776)
Receiving objects: 100% (2989/2989), 22.39 MiB | 39.40 MiB/s, done.
Resolving deltas: 100% (1035/1035), done.
Checking out files: 100% (1900/1900), done.
```

>之后每次 pull 拉取最新的代码
    

2.根据 bat 脚本设定定时任务
文件路径: E:\github\DataMachine\iProject\wdf\定时任务\bat


3.git版本回退
git reset --hard d06ca3c  本地仓库版本回退
git push -f origin   远程仓库版本回退


4.git 自动化拉取代码

```
The gitlab has a lot of tokens:

Private token
Personal Access Token
CI/CD running token
I tested only the Personal Access Token using GitLab Community Edition 10.1.2, the example:

git clone https://gitlab-ci-token:${Personal Access Tokens}@gitlab.com/username/myrepo.git


git clone https://oauth2:${Personal Access Tokens}@gitlab.com/username/myrepo.git
or using username and password:

git clone https://${username}:${password}@gitlab.com/username/myrepo.git
or by input your password:

git clone https://${username}@gitlab.com/username/myrepo.git
But the private token seems can not work.
```


5.git解决冲突
git checkout master
git status 查看状态
git stapy
git stash
git pull origin master
git stash apply
git status
git stash list
git push origin master

> 参考文章：https://www.cnblogs.com/tocy/p/git-stash-reference.html

6. git提示error setting certificate verify locations解决办法
git config --system http.sslverify false
