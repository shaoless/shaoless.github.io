# 如何开启FTP服务

## Linux

```shell
sudo apt-get install vsftpd
```

下载vsftpd后会在/etc/vsftpd.conf文件中有详细的配置说明，修改配置文件后重启vsftpd服务：

### 常用配置

```config
listen=YES  #开启FTP服务。
local_enable=YES  #允许本地用户登录FTP服务器。
write_enable=YES  #允许上传文件。
chroot_local_user=YES  #限制用户只能在自己的目录下访问。
allow_writeable_chroot=YES  #允许用户在自己的目录下创建子目录。
anonymous_enable=NO  #禁止匿名用户登录。
anon_root=/var/ftp/pub  #设置匿名用户的根目录。
dirmessage_enable=YES  #显示目录列表。
xferlog_enable=YES  #记录上传下载日志。
xferlog_file=/var/log/vsftpd.log  #设置日志文件路径。
xferlog_std_format=YES  #日志格式化。
listen_address=192.168.1.100  #设置FTP服务器监听地址。
listen_port=21  #设置FTP服务器监听端口。
pam_service_name=vsftpd  #设置PAM服务名。
userlist_enable=YES  #启用用户列表。
userlist_file=/etc/vsftpd/user_list  #设置用户列表文件路径。
tcp_wrappers=YES  #启用TCP Wrappers。
```

### 重启vsftpd服务

```shell
sudo systemctl restart vsftpd
```

### 配置FTP用户

```shell
sudo useradd -d /home/ftpuser -m ftpuser # 创建ftp用户

sudo passwd ftpuser # 设置ftp用户密码
```

### 创建FTP目录

```shell
sudo mkdir /var/ftp/pub # 创建公共目录
sudo mkdir /var/ftp/user # 创建用户目录

sudo chown ftpuser:ftpuser /var/ftp/pub # 设置ftp用户对公共目录的权限
sudo chown ftpuser:ftpuser /var/ftp/user # 设置ftp用户对用户目录的权限

sudo chmod 755 /var/ftp/pub # 设置公共目录的权限为755
sudo chmod 755 /var/ftp/user # 设置用户目录的权限为755

sudo systemctl restart vsftpd # 重启vsftpd服务
```

> 每个数字可以是0到7之间的任意值，具体含义如下：

- 0：没有权限（---）
- 1：执行权限（--x）
- 2：写权限（-w-）
- 3：写和执行权限（-wx）
- 4：读权限（r--）
- 5：读和执行权限（r-x）
- 6：读和写权限（rw-）
- 7：读、写和执行权限（rwx）

## Windows

### 开启FTP服务

1. 打开控制面板，找到“程序与功能”，点击“关闭与打开Windows功能”管理。
2. 勾选“Internet Information Services”的“FTP Server”选项，然后点击“确定”安装FTP服务。关闭管理。

### 新建Window FTP服务用户

1. 打开“控制面板”并进入“用户账户”。
2. 点击“管理其他账户”，然后点击“添加新用户账户”。
3. 输入用户名和密码，并选择用户类型为“标准用户”。
4. 点击“创建账户”。

### 开启万维网服务和Web管理管理工具

1. 同开启FTP服务一样
