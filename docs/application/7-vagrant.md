
### 流程
> 参考链接: 
http://www.cnblogs.com/alexyang8/p/3380936.html     
https://www.jianshu.com/p/3087304fa3a5

1.安装软件
- 安装Provider virtualbox  
选择电脑适配版本:VirtualBox-6.0.6-130049-Win.exe  
扩展包:VirtualBox 6.0.6 Oracle VM VirtualBox Extension Pack  

- 安装 vagrant 
选择电脑适配版本:vagrant_2.2.4_x86_64.msi

- 注意:Windows7 下需要安装 Windows6.1-KB2819745-x64-MultiPkg.msu


2.配置vagrant
- 创建项目  & 初始化项目 
终端命令: 
```
# ==> 创建目录
mkdir vgtest  
# ==> 切换到文件夹下面
cd vgtest  
# ==> 将centos7v4.box 移动到vgtest文件夹
# ==> 添加box
vagrant box add centos7 centos7v4.box --force    
# 运行完命令后，我们应该会发现在当前目录下出现了Vagrantfile文件 
# 将 文件夹的vgtest/Vagrantfile文件 进行替换
# ==> 初始化
vagrant init centos7   
# ==> 启动实例
vagrant up   
# ==> SSH登陆 
vagrant ssh
``` 

- vagrant 常用终端命令: 
```
# ==> 将centos7v4.box 移动到vgtest文件夹
# ==> 添加box
vagrant box add centos7 centos7v4.box --force    
# 运行完命令后，我们应该会发现在当前目录下出现了Vagrantfile文件 
# 将 文件夹的vgtest/Vagrantfile文件 进行替换
# ==> 初始化
vagrant init centos7   
# ==> 启动实例
vagrant up   
# 查看box列表
vagrant box list  
# ==> SSH登陆 
vagrant ssh
# suspending，暂停虚拟机，保存虚拟机当前的状态（内存和磁盘均不释放），可以使用vagrant up命令恢复运行；
vagrant suspending
# halt，关机，虚拟机停止运行，但是虚拟机实例保留，不销毁，可以理解为是正常的关机；
vagrant halt 
# destroy，销毁虚拟机，虚拟机的实例被销毁;
vagrant destroy
# 重启
vagrant reload
```

### - 配置provisioning脚本  

终端命令: 
```
vim Vagrantfile   
# 添加信息如下
Vagrant.configure("2") do |config|
    config.vm.box = "centos7"
    config.vm.network "private_network", ip: "192.168.33.10"
    config.ssh.username = "root"
    config.ssh.password = "vagrant"

config.vm.synced_folder "../data", "/vagrant_data",
    create: true, owner: "root", group: "root"
```

3.修改 hosts   
文件路径: C:\Windows\System32\drivers\etc\hosts    
```
0.0.0.0 account.jetbrains.com
192.168.33.10 vagrant
```

4.yum
> Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

```
yum install gcc libffi-devel python-devel openssl-devel
```

5.共享的目录
默认情况下，vagrant会共享我们的项目目录，在项目的虚拟机里面，会有一个跟我们的项目的目录是同步的。这样我们可以在本地的电脑上去编辑项目的文件，然后在虚拟机上运行它们。
// 虚拟机内的'/vagrant'目录和本地的项目目录同步
 default: /vagrant => F:\vgtest

6. 配置共享目录
如果有额外的目录要跟虚拟机同步的话，可以通过修改Vagrantfile这个文件，去添加这些额外的共享目录。
```
vim Vagrantfile
config.vm.synced_folder "../data", "/vagrant_data",
    create: true, owner: "root", group: "root"
    // creat:如果本地data文件夹不存在，就会创建
    // owner:目录的拥有者
    // group:所属群组
```

### win10 报错
```
vagrant up启动virtual box 发现报错：VT-x/AMD-V 硬件加速在您的系统中不可用。您的 64-位虚拟机将无法检测到 64-位处理器，从而无法启动。
处理步骤：
1.重启电脑进入BIOS，找到CPU Configuration> Intel Virtualization Technology，设置为Enabled。
2.保存退出，重启电脑，再次启动虚拟机。
3.一切正常了。

你好，华硕intel主板bios设置详细步骤：
1、首先到英特尔网站查询到这款CPU的虚拟化支持特性
2重启，按F8进入bios设置，由于开启了EZ模式，首先进入的是一个图形化的主页，显示相关配置和信息，直接点右上角的退出/高级模式，选择高级模式
3进入bios设置界面，首先看到的是main（概要）选项卡，默认语言是English，当然也可以手动选择为简体中文
4选择进入Advanced高级——CPU   Configuration处理器设置
找到Intel   Virtualization   Technology英特尔虚拟化技术   选项，设置为Enabled开启，再F10保存退出即可。
希望对你有帮助。

5.然后替换成最新的Vagrantfile,然后运行vagrant reload

```

6.Vagrantfile
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  #config.vm.network "private_network", ip: "192.168.33.10"


  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "../data", "/vagrant_data",
  	create: true, owner: "root", group: "root"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  config.ssh.username = "root"
  config.ssh.password = "vagrant"

end

```
