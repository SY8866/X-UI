1:开启root登入
sudo su
cd /root

修改authorized_keys文件（即ssh证书）
vi .ssh/authorized_keys
把ssh-rsa之前的文件都删除掉.
编辑ssh配置文件
nano /etc/ssh/sshd_config
找到PermitRootLogin, 把前面的#去掉 改成下面这样
PermitRootLogin yes
ctrl+x 保存退出 选择y 然后回车
然后
reboot
重启服务器。就可以使用root用户名配合秘钥登入了

2:开启防火墙
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F
apt-get purge netfilter-persistent

reboot 重启



XRAY搭建教程

准备工作
1、VPS 准备 Debian 9+
2、域名解析到VPS 的IP 上

申请SSL证书

apt update -y
apt install -y curl
apt install -y socat
curl https://get.acme.sh | sh
~/.acme.sh/acme.sh --register-account -m xxx@xxx.xxx

#更换真实邮箱成功率更高

~/.acme.sh/acme.sh --issue -d xxxx.xxxx.xxx --standalone

更换你的解析域名

~/.acme.sh/acme.sh --installcert -d xxxx.xxxx.xxx --key-file /root/private.key --fullchain-file /root/cert.crt

更换你的解析域名，此步完成后会在VPS root 目录下看到
证书公钥/root/cert.crt 及 密钥文件/root/private.key

安装BBR (视频中说此代码没用 但经网友测试貌似是有作用的！大家可以安装一下)
但别随便乱用网速搜的BBR脚本，很容易搞崩！！！

echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
lsmod | grep bbr

#打完最后一步，应看到 20480 或 16384 说明BBR 开启成功

安装X-ui面板

bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)

私钥路径：/root/private.key
公钥路径：/root/cert.crt
