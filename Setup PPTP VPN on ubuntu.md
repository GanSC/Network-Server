![GSC](http://www.it.com.cn/f/dc/058/11/quanjing.jpg)    

**This guide has been tested with Ubuntu 12.4 Server.** 
##Setup PPTP Server

1. **First we need to install pptp server using apt-get**

    `sudo apt-get install pptpd`

2. **Then we need to configure the pptpd.**

	`sudo vim /etc/pptpd.conf`  
	`localip 192.168.0.1`    **Current host IP**    
	`remoteip 192.168.0.100-200`  **Client IP**  

3. **Configure DNS servers to use when clients connect to this PPTP server**

    `sudo nano /etc/ppp/options`  
	`ms-dns 8.8.8.8`
	`ms-dns 8.8.4.4`  

4. **Add Server Name**
	`vim pptpd-options`
	`name 172.16.17.63`
	  
5. **Now add a VPN user in /etc/ppp/chap-secrets file.**   
	`vim chap-secrets`  
    `# client        server  secret                  IP addresses`  
	`username		*		password				*`

6. **Check the port operation**  
	`netstat -tnlpu |grep pptpd`        *You can see port 1723 accounts*  

7. **Edit Start the script in /etc/ppp/pptp_vpn file**    
	*  Enable IP Forwarding  
		`echo 1 > /proc/sys/net/ipv4/ip_forward`
	*  Start pptpd_vpn Server  
		`/etc/init.d/pptpd restart`
	*  Set firewall rule and MTU value
		`iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE`
		`iptables -A FORWARD -p tcp --syn -s 192.168.0.0/24 -j TCPMSS --set-mss 1356` 

8. **Edit Stop the script**   
	*  Enable IP Forwarding  
		`echo 0 > /proc/sys/net/ipv4/ip_forward`
	*  Start pptpd_vpn Server  
		`/etc/init.d/pptpd stop`
	*  Set firewall rule  
		`iptables -t nat -F POSTROUTING`

##有问题反馈
在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

* 邮件(king@163.com)
* QQ: 573247450
* weibo: [@失意的多啦](http://kinggsc.blog.163.com/)