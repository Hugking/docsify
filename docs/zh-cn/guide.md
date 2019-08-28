# 标题
## 副标题 
**应用场景**

本地开发的业务代码需要在服务器上测试，之前本人的做法都是将本地代码打包并通过 ftp 传输至服务器解压缩，发现每次都太繁琐了，并且改动不易观察出来，于是便想到了 git 远程仓库，为什么不在服务器上创建一个版本库与本地同步，然后就发现了 `git hooks` ,实现自动部署代码，异常方便，记录下来这个简单的流程。
搭建` Git 服务器`需要准备一台运行 Linux 的机器，强烈推荐用 Ubuntu 或 Debian ，这样，通过几条简单的 `apt` 命令就可以完成安装。假设你已经有 `sudo` 权限的用户账号，下面，正式开始安装。
### 1. 服务器安装 `git` 
```
$ sudo apt-get install git
```
### 2. 创建一个 `git` 用户用来运行 `git` 服务
```
$ sudo adduser git
```
- 当然你也可以创建你想管理 git 的用户名
### 3. 创建证书登录
收集所有需要登录的用户客户端的公钥，就是他们自己的 id_rsa.pub 文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`  文件里，一行一个，下次你用git时就不需要输入用户名和密码了。
###4. 服务器初始化`Git`仓库：
先选定一个目录作为Git仓库，假定是 `/home/git/code/test.git` ，在目录下输入命令：
```
$ sudo git init --bare test.git
```
Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以 `.git` 结尾。然后，把 owner 改为 `git` ：
```
$ sudo chown -R git:git test.git
```
一个空的git仓库就在服务器上建好了，仓库的地址为(可以本地测试一下 `git clone` 这个远程仓库):
 `ssh://git@你的服务器ip:/home/git/code/test.git`
### 5. 网站的根目录 `git clone` 服务器仓库
```
$ cd /var/www/html
$ sudo git clone /home/git/code/test.git
$ sudo chmod -R 777 ./test
```
### 6.为远程仓库设置 `hook` 
```
$ cd /home/git/code/test.git/hooks
$ vim post-receive 
```
*post-receive*
```
#!/bin/sh
unset GIT_DIR
DeployPath="/var/www/html/test"
cd $DeployPath
git pull origin master
echo "推送完成"
exit 0
```
为脚本添加可执行权限
```
$ sudo chmod +x post-receive
```
### 7. 客户端添加服务器远程仓库
以后往这个服务器远程仓库 `push` 代码时，就会自动触发上面的脚本。
```
$ git remote add test git@你的服务器ip:/home/git/code/test.git //添加远程仓库并命名为 test
$ git push test master
```
### 8. 后续代码的更新：
 1. github 有更新的时候 `pull` 更新本地部署仓库
 2. 然后本地先 `push` 到测试服务器进行测试
 3. 测试通过之后 `push` 到正式服务器进行上线
 4. 代码的回滚:
               * 服务器端回滚：推荐 `git reset --hard HEAD^`
                 本地仓库回滚： 无需登陆服务器即可实现代码回滚，`git reset HEAD^` 保留代码回滚，然后使用 `git push remote_name local_branch_name -f` 强制推送
5. 状态查询命令 `git status`
### 9. 禁用 shell 登录
出于安全考虑，第二步创建的 `git` 用户不允许登录 shell，这可以通过编辑 `/etc/passwd` 文件完成。找到类似下面的一行：
```
git:x:1001:1001:,,,:/home/git:/bin/bash
```
改为：
```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```
这样, `git` 用户可以正常通过 ssh 使用 git，但无法登录 shell，因为我们为 `git` 用户指定的 `git-shell` 每次一登录就自动退出。
### 拓展
##### 1. 创建证书登录
默认情况下，用户的 SSH 密钥存储在其 ~/.ssh 目录下。 进入该目录并列出其中内容，你便可以快速确认自己是否已拥有密钥：
```
[root@localhost ~] ls ~/.ssh
authorized_keys  id_rsa.pub
```
- *在服务器端打开 RSA 认证*
在文件 `/etc/ssh/sshd_config` 中添加下列三行内容：
```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile  .ssh/authorized_keys
```
- *服务器导入 id_rsa.pub*
```
[root@localhost ~] cd ~git/.ssh
[root@localhost .ssh] cat id_rsa.pub >> authorized_keys
[root@localhost ~] cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCyQ6mcBiyiYiScdh9hBua8cXoOx59VVudyAkA+m+Gc+hUF09oKAyf5LlI1RJkbobX90L3afUexqnHT+hka1oaX4Gu7tfHYu7nJyGVPcteebJ14wNec750kUH0sS+f87U+Sb37Ynmh/FCCTUU+m/goimH5oe/gH8uSh3mFBlA+NKcBPRWCx7W44L5MK4YqcbddmjXsp+JAO6tHaYBn3GnLB3UzLbQHX222AGO6nByHNBmRHMXePaIzH76zWiy/OjiciJzRon/riftO+O+qOA9/+ZoB0KzycA0MeEOwqx5iWwRHzx8WrYufC9PZdvlKe/a4KxSG1XA15y69y0dFfl0CL root@localhost.localdomain
```
#####2.Linux 上用户创建与删除
 ```
[root@localhost ~] adduser haha   # 创建用户 haha 是用户名
[root@localhost ~] passwd haha   # 为该用户设置密码
[root@localhost ~] userdel -r haha # 完全删除用户
[root@localhost ~] find / -name "*haha" # 再使用 find 命令查看，用户相关文件已经删除。
```