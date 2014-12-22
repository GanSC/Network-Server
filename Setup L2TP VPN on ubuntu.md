![GSC](http://www.it.com.cn/f/dc/058/11/quanjing.jpg)  
 
1. PPTP VPN 安全性和NAT穿透性差，我的工作和生活环境有两个路由，还有自建NAT的情况，使用体验慢，效果差-放弃  
2. L2TP IPSec VPN IPSec属于高强度加密，L2TP走UDP，NAT穿透好，只要不封闭目的端的指定端口，一般不会出现墙的情况,所以我选择了 L2TP IPSec VPN来解决我的问题    
3. Linode在东京，国内访问速度在 250毫秒左右，速度属于比较快的国外VPS服务器了，我选择的主机操作系统是Ubuntu 12.04 LTS，属于比较常用的操作系统。  

**This guide has been tested with Ubuntu 12.4 Server.**  

 
**First we need to install Xl2tp and openwan software**    

	`sudo apt-get install openswan xl2tpd`

Linode Ubuntu IPSec 提示是否是有X.509证书    
Linode Ubuntu IPSec 提示是否是有X.509证书,选择否  
Linode Ubuntu IPSec X.509证书 导入还是创建    
Linode Ubuntu IPSec X.509证书 导入还是创建，选择创建    
Linode Ubuntu IPSec X.509是否使用自签名    
Linode Ubuntu IPSec X.509是否使用自签名，选择使用自签名    
Linode Ubuntu IPSec 安装完成    
Linode Ubuntu IPSec 安装完成      


**Then we need to configure /etc/ipsec.conf.**  
`sudo vim /etc/ipsec.conf `

version 2.0  
>**config setup**  
	`nat_traversal=yes`  
	`virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12`  
	`oe=off`  
	`protostack=netkey`  
**conn L2TP-PSK-NAT**  
	`dpddelay=40`  
	`dpdtimeout=130`  
	`dpdaction=clear`  
	`rightsubnet=vhost:%priv`  
	`also=L2TP-PSK-noNAT`  
**conn L2TP-PSK-noNAT**  
	`authby=secret`  
	`pfs=no`  
	`auto=add`  
	`keyingtries=3`  
	`rekey=no`  
	`ikelifetime=8h`  
	`keylife=1h`  
	`type=transport`  
	`left=106.186.178.35`  **You Server IP address**  
	`leftprotoport=17/1701`  
	`right=%any`   
	`rightprotoport=17/%any`  


**Then we need to configure /etc/ipsec.secrets.**  
	`sudo vim /etc/ipsec.secrets `  
设置IPSec握手时的Machine Authentication – Shared Secret,最后一行添加这句
 
	`106.186.178.35 %any: PSK "password"`  

**Then we need to configure /etc/xl2tpd/xl2tpd.conf.** 
	`sudo vi /etc/xl2tpd/xl2tpd.conf`    
>**[global]**  
	`ipsec saref = yes`  
**[lns default]**  
	`ip range = 10.1.2.2-10.1.2.255`  
	`local ip = 10.1.2.1`  
	`;require chap = yes`  
	`refuse chap = yes`  
	`refuse pap = yes`  
	`require authentication = yes`  
	`ppp debug = yes`  
	`pppoptfile = /etc/ppp/options.xl2tpd`  
	`length bit = yes`  


**Then we need to configure /etc/ppp/options.xl2tpd.**   
	 `sudo vi /etc/ppp/options.xl2tpd`  
  
>require-mschap-v2  
ms-dns 8.8.8.8  
asyncmap 0  
auth  
crtscts  
lock  
hide-password  
modem  
debug  
name l2tpd  
proxyarp  
lcp-echo-interval 30  
lcp-echo-failure 4  

**Then we need to configure /etc/ppp/chap-secrets.**  
	`sudo vi /etc/ppp/chap-secrets`    
  	client server secret IP addresses  
	seaman l2tpd password *  

**Then we need to configure iptables.**  
	`sudo vi /etc/rc.local`    
>`iptables --table nat --append POSTROUTING --jump MASQUERADE`  
`echo 1 > /proc/sys/net/ipv4/ip_forward`  
`for each in /proc/sys/net/ipv4/conf/*`  
`do`  
`echo 0 > $each/accept_redirects`  
`echo 0 > $each/send_redirects`  
`done`  
`/etc/init.d/ipsec restart`  
`exit 0`  

**Then we need to Restart Services.**  
	`sudo service ipsec restart`  
	`sudo service xl2tpd restart`  

**Then we need to Reboot the System**  
	`sudo reboot`  

FAQ:如下的错误如何处理  
Jul 15 00:18:58 li600-18 pppd[5800]: The remote system is required to authenticate itself  
Jul 15 00:18:58 li600-18 pppd[5800]: but I couldn’t find any suitable secret (password) for it to use to do so.  

基本的原因是 /etc/ppp/chap-secrets 不正确，常见的错误有：  
1.ipaddress那个*号没填  
2.server应该是option.xl2tpd里面制定的那个name字段，是否少些一个d，例如写成xl2tp而不是xl2tpd  


##有问题反馈
在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

* 邮件(king@163.com)
* QQ: 573247450
* weibo: [@失意的多啦](http://kinggsc.blog.163.com/)