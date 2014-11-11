	
Configure DHCP ( Dynamic Host Configuration Protocol ) Server. If you make your linux computer DHCP server, it's neccesarry to disable DHCP function on router in LAN.  
#Install and configure DHCP  
root@dlp:~# aptitude -y install dhcp3-server  
root@dlp:~# vi /etc/dhcp/dhcpd.conf  
**1. line 16: specify domain name**    
option domain-name "server.world";  
**2. line 17: specify nameserver's hostname or IP address**      
option domain-name-servers dlp.server.world;  
**3. line 24: uncomment**  
authoritative;   
**4. add at the last    
   specify network address and subnet-mask**  
subnet 10.0.0.0 netmask 255.255.255.0 {  
**5. specify default gateway**  
option routers 10.0.0.1;  
**6. specify subnet-mask**    
option subnet-mask 255.255.255.0;  
**7. specify the range of leased IP address**  
range dynamic-bootp 10.0.0.200 10.0.0.254;  
}  
root@dlp:~# service isc-dhcp-server start   
isc-dhcp-server start/running, process 1465  