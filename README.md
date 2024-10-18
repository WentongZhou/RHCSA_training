
# 第一题 配置网络
## 1.1 配置静态IP
```shell
nmcli con show #查看网卡信息
nmcli con mod 'Wired connection 1' ipv4.method manual
ipv4.addresses 172.25.250.100/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.250.254 autoconnect yes # 配置网络信息
nmcli con up 'Wired connection 1' # 重启网络
```
## 1.2 配置主机名
```shell
ssh root@172.25.250.100 # 连接终端
hostnamectl set-hostname node1.domain250.example.com
```
# 第二题 配置yum源
## 2.1 配置本地yum源
```shell
vim /etc/yum.repos.d/rhcsa.repo # 使用vim修改yum源配置文件
```
```shell
[Base]
name=Base
baseurl=http://content/rhel9.0/x86_64/dvd/BaseOS
enabled=1
gpgcheck=no
[App]
name=App
baseurl=http://content/rhel9.0/x86_64/dvd/AppStream
enabled=1
gpgcheck=no
```
# 第三题 配置SELinux
```shell
getenforce # 查看SELinux状态
yum provides "*/semanage"
yum -y install policycoreutils-python-utils
semanage fcontext -m -t httpd_sys_content_t /var/www/html/file1
semanage port -a -t http_port_t -p tcp 82
restorecon -Rv /var/www/html
#也可以使用man semanage port，然后/EXAMPLE ​
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-port=82/tcp
firewall-cmd --reload
systemctl restart httpd
systemctl enable --now httpd
#验证，出现源码即正确(必做操作)​
curl http://node1.domain250.example.com:82/file(1-3)
```

# 第四题 建立用户账户
```shell
groupadd sysmgrs
useradd -G sysmgrs natasha # 添加用户到组
useradd -G sysmgrs harry # 添加用户到组
useradd -s /bin/false sarah 
echo flectrag |passwd --stdin natasha  # 修改密码
echo flectrag |passwd --stdin harry # 修改密码
echo flectrag |passwd --stdin sarah # 修改密码
```

# 第五题 配置cron作业

```shell
systemctl status crond
systemctl enable crond
crontab -e -u harry # harry是用户名，题目要求什么名字就用什么名字
23 14 * * * /usr/bin/echo hello #题型1 每天14:23执行
*/2 * * * * logger “EX200 in progress” #题型2，每隔2分钟记录一次
```

# 第六题 创建协作用户组
```shell
mkdir /home/managers
chgrp sysmgrs /home/managers
chmod 2770 /home/managers
```

# 第七题 配置NTP
```shell
yum -y install chrony
vim /etc/chrony.conf
server materials.example.com iburst # 添加NTP服务器，按题目要求
systemctl restart chronyd
systemctl enable chronyd
```
