---
layout: wiki
wiki: Shell
title: shell
---

#### 开启密码登录
<<<<<<< Updated upstream
=======

```bash
vim /etc/ssh/sshd_config
# 将 PasswordAuthentication no 配置的 no 改为 yes，支持其他用户密码登录
# 如果需要 root 用户密码登录
# 将 PermitRootLogin 配置改为yes
# 重启 sshd 可以用下面命令。也可以用 systemctl restart sshd.service ；
sudo /sbin/service sshd restart
```

>>>>>>> Stashed changes

```bash
vim /etc/ssh/sshd_config
# 将 PasswordAuthentication no 配置的 no 改为 yes，支持其他用户密码登录
# 如果需要 root 用户密码登录
# 将 PermitRootLogin 配置改为yes
# 重启 sshd 可以用下面命令。也可以用 systemctl restart sshd.service ；
sudo /sbin/service sshd restart
```



#### 检测Netflix

```bash
wget -O nf https://github.com/sjlleo/netflix-verify/releases/download/2.61/nf_2.61_linux_amd64 && chmod +x nf && clear && ./nf
```

#### 宝塔纯净版Centos全新安装命令

```bash
yum install -y wget && wget -O install.sh http://v7.hostcli.com/install/install_6.0.sh && sh install.sh
```

#### 宝塔纯净版Debian全新安装命令

```bash
wget -O install.sh http://v7.hostcli.com/install/install-ubuntu_6.0.sh && bash install.sh
```







#### tcp脚本

```bash
wget "https://raw.githubusercontent.com/wejudging/shell/main/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```


#### trojan

```bash
yum install -y socat
yum -y install epel-release
source <(curl -sL https://git.io/trojan-install)
```
```bash
systemctl start trojan  
systemctl restart trojan
systemctl stop trojan
systemctl status trojan
systemctl enable trojan
systemctl stop trojan-web
systemctl disable trojan-web
trojan export ./trojan.sql
trojan import ./trojan.sql
```

#### 青龙必须要安装的依赖
```bash
docker exec -it qinglong bash
curl -fsSL https://ghproxy.com/https://raw.githubusercontent.com/FlechazoPh/QLDependency/main/Shell/QLOneKeyDependency.sh | sh
```

#### firewalld

```bash
systemctl stop firewalld
systemctl disable firewalld
```
#### swap

```bash
wget https://www.moerats.com/usr/shell/swap.sh && bash swap.sh
```
#### azure swap

```bash
dd if=/dev/zero of=/swapfile count=4096 bs=1M
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
vi /etc/fstab
/swapfile   none    swap    sw    0   0
```
#### myip

```bash
ip=$(curl -s http://myip.ipip.net)
echo "My public IP address is: $ip"
```

##### gost

```bash
wget --no-check-certificate -O gost.sh https://raw.githubusercontent.com/KANIKIG/Multi-EasyGost/master/gost.sh && chmod +x gost.sh && ./gost.sh
```

#### danate

```bash
wget --no-check-certificate https://raw.github.com/Lozy/danted/master/install.sh -O install.sh
bash install.sh  --port=18888 --user=weijiajin --passwd=weijiajin

bash install.sh --uninstall

systemctl enable sockd.service

service sockd state

```
#### 增加网卡

```bash
ifconfig eth0:1 10.0.0.5 up
ifconfig eth0:2 10.0.0.6 up
ifconfig eth0:3 10.0.0.7 up
ifconfig eth0:4 10.0.0.8 up
ifconfig eth0:5 10.0.0.9 up
ifconfig eth0:6 10.0.0.10 up
ifconfig eth0:7 10.0.0.11 up
ifconfig eth0:8 10.0.0.12 up
ifconfig eth0:9 10.0.0.13 up
```

#### 一键安装多IP s5

```bash
wget https://github.com/zyhut200/az/archive/refs/heads/main.zip -O /etc/network/main.zip
unzip -oj /etc/network/main.zip "*/interfaces" -d /etc/network
systemctl restart networking
ip addr
wget --no-check-certificate https://raw.github.com/Lozy/danted/master/install.sh -O install.sh
bash install.sh  --port=18888 --user=weijiajin --passwd=weijiajin

```

#### DD系统

```bash
LANG=en_US.UTF-8
wget --no-check-certificate -O AutoReinstall.sh https://git.io/AutoReinstall.sh && bash AutoReinstall.sh
Pwd@CentOS
LANG=en_US.UTF-8
fdisk -l
 df -h
sfdisk -l
LANG=en_US.UTF-8
growpart /dev/vda 1
resize2fs /dev/vda1
```



#### 一键回程三网路由

```bash
curl https://raw.githubusercontent.com/zhucaidan/mtr_trace/main/mtr_trace.sh|bash
```

#### speedtest测速脚本

```bash
wget -O speedtest-cli https://raw.githubusercontent.com/wejudging/shell/main/speedtest.py
chmod +x speedtest-cli
python speedtest-cli
```

#### 生成秘钥

```bash
ssh-keygen -t rsa -b 4096 -C "Deploy Key" -f github-deploy-key -N ""
```

当前目录生成两个文件：
- github-deploy-key —— 私钥
- github-deploy-key.pub —— 公钥

#### 图标生成

```bash
https://img.shields.io/badge/XXX-YYY-f38020?logo=cloudflare&logoColor=f38020&labelColor=282d33
```

#### mac一键开启hdpi
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/xzhih/one-key-hidpi/master/hidpi.sh)"
```
#### x-ui

```bash
bash <(curl -Ls https://raw.githubusercontent.com/sprov065/x-ui/master/install.sh) 0.2.0
```



### 博客更新子模块
```bash
cd /Users/weijiajin/Documents/wejudging.github.io/themes/stellar
git add .
git commit -m "12345"
git push
```

### ServerStatus-Hotaru

服务端安装方法

```bash
wget https://raw.githubusercontent.com/cokemine/ServerStatus-Hotaru/master/status.sh
# wget https://cokemine.coding.net/p/hotarunet/d/ServerStatus-Hotaru/git/raw/master/status.sh 若服务器位于中国大陆建议选择 Coding.net 仓库
bash status.sh s

```

客户端安装方法

```bash
wget https://raw.githubusercontent.com/cokemine/ServerStatus-Hotaru/master/status.sh
# wget https://cokemine.coding.net/p/hotarunet/d/ServerStatus-Hotaru/git/raw/master/status.sh 若服务器位于中国大陆建议选择 Coding.net 仓库
bash status.sh c

```

### 终端开启代理

```
export https_proxy=http://127.0.0.1:8090;export http_proxy=http://127.0.0.1:8090;export all_proxy=socks5://127.0.0.1:8090
```
