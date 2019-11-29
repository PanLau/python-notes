# redis的配置

##### 用homebrew安装redis
##### 使用配置文件启动redis server 
```
$ redis-server /usr/local/etc/redis.conf
```
##### redis 配置文件的位置 
```
/usr/local/etc/redis.conf
```
##### 卸载redis和它的文件 
```
brew uninstall redis rm ~/Library/LaunchAgents/homebrew.mxcl.redis.plist
```
##### 测试redis server是否启动 
```
$ redis-cli ping
```
