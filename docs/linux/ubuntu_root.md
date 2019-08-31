# 一、腾讯讯云主机开启 root 用户登录如下：
+ 腾讯云主机ubuntu 系统默认用户名为 ubuntu
   - 1. 修改root密码
   ```
   sudo passwd root
   Enter new UNIX password: // 输入新密码
   Retype new UNIX password: // 重复密码
   passwd: password updated successfully // 修改成功
   ```
  - 2. 修改 sshd_config 配置如下
   ```
   vi /etc/ssh/sshd_config
   # Authentication:
   LoginGraceTime 120
   PermitRootLogin yes
   StrictModes yes
   ```
  - 3. 最后重启下ssh
  ```
  sudo service ssh restart
  ```
# 二、修改系统语言为中文
+ 修改中文
  - 1. 查看当前语言环境
  ``` echo $LANG ```
  - 2.查看当前系统是否有中文语言包（`zh_CN.utf8`）
  ```locale -a```
  - 3. 安装中文语言包
  ```apt install language-pack-zh-hans```
  - 4. 再次查看是否有中文语言包
  ```locale -a```
  - 5. 修改系统环境变量
  ```vi ~/.bashrc```
  加入下面这一行：
  ```
  LANG="zh_CN.utf8"
  ```
  - 6. 执行修改
  ```source ~/.bashrc ```
  此时已更改成功
# 三、更新系统（可选）
  ```
   apt-get update 
   apt-get dist-upgrade
  ```

