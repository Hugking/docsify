**应用说明**
腾讯云主机 ubuntu 系统默认用户名为 ubuntu，修改文件需要 root 权限太过繁琐，为了方便改成 root 用户登录
## 一、腾讯讯云主机开启 root 用户登录如下：
+ 腾讯云主机ubuntu 系统默认用户名为 ubuntu
- 1.修改root密码
```bash
sudo passwd root
Enter new UNIX password: // 输入新密码
Retype new UNIX password: // 重复密码
passwd: password updated successfully // 修改成功
```
- 2.修改 sshd_config 配置如下
```bash
vi /etc/ssh/sshd_config
# Authentication:
LoginGraceTime 120
PermitRootLogin yes
StrictModes yes
```
- 3.最后重启下ssh
```bash
sudo service ssh restart
```
## 二、修改系统语言为中文
- 1.查看当前语言环境
``` bash
echo $LANG 
```
- 2.查看当前系统是否有中文语言包（`zh_CN.utf8`）
```bash
locale -a
```
- 3.安装中文语言包
```bash
apt install language-pack-zh-hans
```
- 4.再次查看是否有中文语言包
```bash
locale -a
```
- 5.修改系统环境变量
```bash
vi ~/.bashrc
```
加入下面这一行：
```bash
LANG="zh_CN.utf8"
```
- 6.执行修改
```bash
source ~/.bashrc
```
此时已更改成功

## 三、更新系统（可选）
```bash
apt-get update 
apt-get dist-upgrade
```

