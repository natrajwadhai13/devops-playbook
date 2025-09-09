---
title: • IMP Command
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 1
has_children: true
---
## IMP Command:

tar -cvzf /bkp/warbackup/AHD_Full_APP_BACKUP_09_01_2024.tar.gz /data/jboss-6.1.0.Final

tar -cvzf /bkp/warbackup/RDD_Full_APP_BACKUP_11_01_2024.tar.gz /data/wildfly-10.0.0.Final/

chage -M -1 username

dlsb_release -a or cat /etc/*release or cat /etc/issue* or cat /proc/version.

nproc

yum install sysstat

mpstat -Cpu Utilization

prefetch

history -c

history -d 

dmesg

!996

jstat -gc <PID>    for heap memory

jmap -heap <PID>   for heap memory

crontab allow
echo justin > /etc/cron.allow
/etc/cron.allow
/etc/cron.deny

==================================================================

```bash
history -d <line number> 
hostnamectl set-hostname SFTP_APP1
copy via date: for i in `ls -lrt | grep "Sep" | awk '{print $9}' `; do cp -p $i* /Vol2/custsbicmp_Archive_bkp_last_3month/; done
Move via date: for i in `ls -lrt | grep "Aug" | awk '{print $9}' `; do mv $i* /Vol2/custsbicmp_Archive_bkp_last_3month/; done
sed -i -e 's/\r$//' jboss_restart.sh
```
==================================================================
```bash

1. Clear PageCache only.
# sync; echo 1 > /proc/sys/vm/drop_caches
2. Clear dentries and inodes.
# sync; echo 2 > /proc/sys/vm/drop_caches
3. Clear pagecache, dentries, and inodes.
# sync; echo 3 > /proc/sys/vm/drop_caches 
sftp sftp@124.153.117.33

# ps -ef | grep java
# kill -9 Pid
```
==================================================================

```bash
cd /data/jboss-eap-6.3/bin/
#nohup sh standalone.sh -b 0.0.0.0 > /dev/null 2>&1 &
NOTE IF JBOSS 6.1 THEN to start jboss 6.1
cd /data/jboss-6.1.0.Final/bin/
nohup sh run.sh -b 0.0.0.0 > /dev/null 2>&1 &
#    
crontab -u postgres -e
*/2 * * * * /bkp/postgres_bkp.sh
Tomcat
n^Cup sh standalone.sh -b 0.0.0.0  > /dev/null 2>&1 &

export  CATALINA_BASE=/data/tomcat/apache-tomcat-8.5.50

export  CATALINA_HOME=/data/tomcat/apache-tomcat-8.5.50

export  CATALINA_BASE=/vol2/server/tomcat/apache-tomcat-8.5.50
export  CATALINA_HOME=/vol2/server/tomcat/apache-tomcat-8.5.50

./catalina.sh start

# systemctl enable postgresql-10.service
# systemctl start postgresql-10.service
```
==================================================================
```bash
iostat
shell:startup
curl ifconfig.co
alternatives --install /usr/bin/java java  /usr/lib/jvm/jdk1.8.0_144/bin/java 1

sudo update-alternatives --remove "java" "/usr/lib/jvm/jdk1.8.0_291/bin/java"
alternatives --config java
java -version
JAVA_HOME="/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.191.x86_64"
JAVA_HOME="/usr/lib/jvm/jdk1.8.0_144"
sudo update-alternatives --set java /usr/lib/jvm/jdk1.8.0_version/bin/java 1

JAVA_HOME="/usr/java/jdk1.8.0_144"
12
# crontab -l
crontab -u webadmin -l
how to get heap size in linux
#ps -ef | grep java | grep Xmx.
# date -s "Mon Jan 31 15:29:20 IST 2022"
./WEB-INF/classes/ApplicationDB.properties
```
```bash
=================================================================
firewall-cmd --add-port=port-number/port-type
firewall-cmd --zone=public --permanent --add-port=50000/tcp
firewall-cmd --zone=public --permanent --add-port=4990-4999/udp
firewall-cmd --zone=public --permanent --remove-port=8080/udp
firewall-cmd --zone=public --permanent --list-ports
sudo firewall-cmd --reload
systemctl disable firewalld
systemctl enable firewalld
systemctl start firewalld
systemctl status firewalld
service iptables status
netstat -tulpn
netstat -ant | grep 55000
nmap 124.153.117.33 -p 22
nmap 192.168.0.30 -p 22
nc -zvw10 192.168.0.1 22
telnet 
tracert -d 115.124.116.110
ss

sudo lsof -i -P -n | grep LISTEN
sudo netstat -tulpn | grep LISTEN
sudo ss -tulpn | grep LISTEN
sudo lsof -i:22 ## see a specific port such as 22 ##
sudo nmap -sTU -O IP-address-Here
=================================================================


vi /etc/ssh/sshd_config
systemctl restart sshd
service sshd restart
ssh-keygen
ssh-copy-id -i


dcdiag /test:dns
dcdiag

scp db2inst1@10.187.201.238:/bkp/online_backup/delta_backup/incr_delta_bkpbkp.log /root/
scp -P 2322 file.txt remote_username@10.10.0.2:/remote/directory
scp db2inst1@10.187.202.211:/bkp/online_backup/delta_backup/incr_delta_bkpbkp.log /root/
scp -r FRM_AUDIT_TRAIL_TRIBAL.msg 10.187.202.211:/root/

scp nohup.out root@13.127.122.161:/vol1
==================================================================
vim /etc/bashrc
alias mps='free -h'
source /etc/bashrc
alias -p
unalias mps
unzip MJP.war -d MJP1.war
if error is come ( -bash: ./jboss_restart.sh: /bin/sh^M: bad interpreter: No such file or directory)
sed -i -e 's/\r$//' jboss_restart.sh

alias cdstand='cd /data/jboss-6.1.0.Final/server/default/'
alias rmlog='rm -rf data log tmp'
alias cdbin='cd /data/jboss-6.1.0.Final/bin/'
alias wnohup='nohup sh run.sh -b 0.0.0.0 > /dev/null 2>&1 &'
alias psjava='ps -ef | grep java'
==================================================================

Please find attached cacerts file and upload on below path 
jdk1.7/jre/lib/security/cacerts

Xbootclasspath/p:$JBOSS_HOME/bin/alpn-boot-8.1.8.v20160420-javadoc.jar

mpstat -P ALL
mpstat -P ALL 2 3

https://who.is/


db2 terminate
db2 force application all
db2 list active databases
db2stop
db2start
nohup db2 backup db salaarth to /bkp/full_offline_backup compress &
db2 connect to salaarth
db2 LIST UTILITIES show detail
nohup db2 RESTORE DATABASE IFMSMIGR FROM /Vol1/Backup TAKEN AT 20210131113008 on /Vol2/ifmsmigr/db2inst1 &
db2ckbkp SALAARTH.0.db2inst1.NODE0000.CATN0000.20231103010003.001

==================================================================


Zero SSL :- 



openssl pkcs12 -export -out customizedsevaarth.mahaitgov.in.pfx -inkey private.key -in certificate.crt   ----working 
openssl pkcs12 -export -out customisedsevaarth.mahaitgov.in.p12 -inkey private.key -in certificate.crt -passin pass:12345 -passout pass:12345 --- Working 
openssl pkcs12 -export -out mjptest.mahaitgov.in.pfx -inkey private.key -in certificate.crt

openssl req -new -newkey rsa:2048 -nodes -keyout mjpsevaarth.in.key -out mjpsevaarth.in.csr

IN MH mumbai
mjp sevaarth
IT
domain name
email id
password

openssl pkcs12 -export -out stag.mjpsevaarth.in.pfx -inkey private.key -in certificate.crt   --- for staging mjp server
sudo openssl pkcs12 -export -out wildfly-pkcs12.pfx -in mydomain.crt -inkey server-prv.key -certfile ca-cert.crt
sudo openssl pkcs12 -export -out mjpsevaarth.in.pfx -in EndEntity_mjpsevaarth.in.cer -inkey mjpsevaarth.in.key -certfile 'RootCA_emSign Root CA - G1.cer'
sudo openssl pkcs12 -export -out mjpsevaarth.in.pfx -in EndEntity_mjpsevaarth.in.cer -inkey mjpsevaarth.in.key -certfile 'CA_emSign SSL CA - G1.cer'

sudo openssl pkcs12 -export -out mahaithrmslive.mahaitgov.in.pfx -in certificate.crt -inkey private.key -certfile ca_bundle.crt
sudo openssl pkcs12 -export -out jalsevaarth.mahaitgov.in.pfx -in certificate.crt -inkey private.key -certfile ca_bundle.crt

MJP Live Application

openssl req -new -newkey rsa:2048 -nodes -keyout mjpsevaarth.in.key -out mjpsevaarth.in.csr ...... server csr certificte
openssl pkcs12 -export -out certificate.pfx -inkey mjpsevaarth.in.key -in bd7a731b4b83c315.crt ........ for pfx file

==================================================================

ACL Command.

setfacl -m user:russell:rwx /work/demoacltest.txt
setfacl -b demoacltest.txt
setfacl -R -m u:colleague:rwX 
getfacl

Host entery 
# vi /etc/hosts

Why /etc/resolv.conf and /etc/hosts files are used?
[root@MHSDC-RDD-WEB1 deployments]# cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 10.187.203.101
nameserver 10.187.202.28

=====================================================
Client Certificate import in server
=====================================================

Youtube link

https://youtu.be/q0r4MQCsj8w

---------------------------------------------------------------------------------

crt to cer to x509 certificate

# openssl x509 -inform PEM -in Root.crt  -inkey cra-nsdl.com.key -out nsdl.cer

RDD Server
openssl x509 -inform PEM -in Root.crt -out nsdl.cer
--------------------------------------------------------------------------
# /usr/lib/jvm/jdk1.8.0_144/bin/keytool -keystore  cacerts  -importcert -file  /tmp/cert-nsdl/nsdl.cer

RDD server 
/usr/lib/jvm/jdk1.8.0_144/bin/keytool -keystore  cacerts  -importcert -file /tmp/Beams_crt/www.mahakosh.gov.in.crt    .... working

/usr/lib/jvm/jdk1.8.0_144/jre/lib/security/cacerts -keystore  cacerts  -importcert -file /mnt/www.mahakosh.gov.in.crt

keytool -importcert -keystore /usr/lib/jvm/jdk1.8.0_144/jre/lib/security/cacerts -storepass changeit -file /mnt/www.mahakosh.gov.in.crt -alias "root_cert"

keytool -importcert -keystore /usr/local/java/jdk1.8.0_60/jre/lib/security/cacerts -storepass changeit -file ~/Downloads/cert_file.crt -alias "root_cert"

password - Mahait@123
#/usr/lib/jvm/jdk1.8.0_144/bin/keytool -keystore  cacerts  -importcert -file /root/121.240.64.237.crt

Certificate was added to keystore


SSL certificate import command as below

Step 1 : Go to ./jre/lib/security/ 

Step 2 : Take backup cacerts file

step 3 :  execute below command as below

keytool -import -trustcacerts -alias mdecert -file PATH\maharashtra.gov.in.crt -keystore cacerts
 
Prompt for passwrod 

pasword : changeit

--------------------------------------------------------------------
echo 'export http_proxy=http://cra-nsdl.com:80' | sudo tee -a /etc/profile.d/proxy.sh
echo 'export https_proxy=https://cra-nsdl.com:443' | sudo tee -a /etc/profile.d/proxy.sh
echo 'export no_proxy=http://localhost' | sudo tee -a /etc/profile.d/proxy.sh

sudo dpkg-reconfigure ca-certificates
ls -l /etc/ssl/certs | grep -i nsdl.cer

echo 'export no_proxy=http://localhost' | sudo tee -a /etc/profile.d/proxy.sh

```