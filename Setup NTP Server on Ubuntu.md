#Configure NTP server for time adjustment.
root@dlp:~# aptitude -y install ntp  
root@dlp:~# vi /etc/ntp.conf  
1. line 19: make it comment  
+ #server 0.ubuntu.pool.ntp.org  
+#server 1.ubuntu.pool.ntp.org  
+#server 2.ubuntu.pool.ntp.org  
+#server 3.ubuntu.pool.ntp.org  
+#server ntp.ubuntu.com  
+#change servers for synchronization  
server ntp1.jst.mfeed.ad.jp  
server ntp2.jst.mfeed.ad.jp  
server ntp3.jst.mfeed.ad.jp  
2. line 48: add the network range you allow to receive requests  
restrict 10.0.0.0 mask 255.255.255.0 nomodify notrap  
root@dlp:~# /etc/init.d/ntp restart   
 * Stopping NTP server ntpd  
   ...done.  
 * Starting NTP server ntpd  
   ...done.  
   
root@dlp:~# ntpq -p # check working    
     remote           refid      st t when poll reach   delay   offset  jitter  
==============================================================================  
 ntp1.jst.mfeed. 133.243.236.17   2 u    3   64    1   20.052    2.622   0.000  
 ntp2.jst.mfeed. 210.173.160.86   2 u    2   64    1   19.616    2.274   0.000  
 ntp3.jst.mfeed. 210.173.160.56   2 u    1   64    1   18.126    1.942   0.000  
 europium.canoni .INIT.          16 u    -   64    0    0.000    0.000   0.000