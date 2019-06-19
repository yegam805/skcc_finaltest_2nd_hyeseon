15.164.126.247    172.31.15.8
15.164.131.212    172.31.13.189
15.164.131.84     172.31.3.229
15.164.132.44     172.31.11.25
15.164.135.119    172.31.6.157


# 1. Enable user / password login for each of the 5 nodes

## a. Create a password for user “centos”
sudo passwd centos

```
[centos@ip-172-31-15-8 ~]$ sudo passwd centos
Changing password for user centos.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.

```

## b. Modify sshd_config to allow password login
sudo vi /etc/ssh/sshd_config
패스워드 인증을 허용

```
.....
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication yes
....
```

## c. Restart the sshd.service
sudo systemctl restart sshd.service

# 2. Setup /etc/hosts with the following information for each of the 5 hosts

## a. Private_IP FQDN Shortcut
sudo vi /etc/hosts

```
172.31.15.8     ts1.test.com    ts1
172.31.13.189   ts2.test.com    ts2
172.31.3.229    ts3.test.com    ts3
172.31.11.25    ts4.test.com    ts4
172.31.6.157    ts5.test.com    ts5
```

# 3. Change the hostname as necessary to the FQDN that you setup above
sudo hostnamectl set-hostname ts1.test.com

sudo hostnamectl set-hostname ts2.test.com

sudo hostnamectl set-hostname ts3.test.com

sudo hostnamectl set-hostname ts4.test.com

sudo hostnamectl set-hostname ts5.test.com

hostname -f

```
[centos@ip-172-31-15-8 ~]$ sudo hostnamectl set-hostname ts1.test.com
[centos@ip-172-31-15-8 ~]$ hostname -f
ts1.test.com
```
```
[centos@ip-172-31-15-8 ~]$ sudo reboot
```

# 4. Install JDK on each of the hosts (you may choose to install on just the host where you will install CM and use the Wizard later to install on the rest

yum list java*jdk-devel
```
[centos@ts1 ~]$ yum list java*jdk-devel
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
Available Packages
java-1.6.0-openjdk-devel.x86_64        1:1.6.0.41-1.13.13.1.el7_3        base
java-1.7.0-openjdk-devel.x86_64        1:1.7.0.221-2.6.18.0.el7_6        updates
java-1.8.0-openjdk-devel.i686          1:1.8.0.212.b04-0.el7_6           updates
java-1.8.0-openjdk-devel.x86_64        1:1.8.0.212.b04-0.el7_6           updates
java-11-openjdk-devel.i686             1:11.0.3.7-0.el7_6                updates
java-11-openjdk-devel.x86_64           1:11.0.3.7-0.el7_6                updates
[centos@ts1 ~]$

```
sudo yum install java-1.8.0-openjdk-devel.x86_64
```
Installed:
  java-1.8.0-openjdk-devel.x86_64 1:1.8.0.212.b04-0.el7_6

Dependency Installed:
  alsa-lib.x86_64 0:1.1.6-2.el7                                    atk.x86_64 0:2.28.1-1.el7
  avahi-libs.x86_64 0:0.6.31-19.el7                                cairo.x86_64 0:1.15.12-3.el7
  copy-jdk-configs.noarch 0:3.3-10.el7_5                           cups-libs.x86_64 1:1.6.3-35.el7
  dejavu-fonts-common.noarch 0:2.33-6.el7                          dejavu-sans-fonts.noarch 0:2.33-6.el7
  fontconfig.x86_64 0:2.13.0-4.3.el7                               fontpackages-filesystem.noarch 0:1.44-8.el7
  fribidi.x86_64 0:1.0.2-1.el7                                     gdk-pixbuf2.x86_64 0:2.36.12-3.el7
  giflib.x86_64 0:4.1.6-9.el7                                      graphite2.x86_64 0:1.3.10-1.el7_3
  gtk-update-icon-cache.x86_64 0:3.22.30-3.el7                     gtk2.x86_64 0:2.24.31-1.el7
  harfbuzz.x86_64 0:1.7.5-2.el7                                    hicolor-icon-theme.noarch 0:0.12-7.el7
  jasper-libs.x86_64 0:1.900.1-33.el7                              java-1.8.0-openjdk.x86_64 1:1.8.0.212.b04-0.el7_6
  java-1.8.0-openjdk-headless.x86_64 1:1.8.0.212.b04-0.el7_6       javapackages-tools.noarch 0:3.4.1-11.el7
  jbigkit-libs.x86_64 0:2.0-11.el7                                 libICE.x86_64 0:1.0.9-9.el7
  libSM.x86_64 0:1.2.2-2.el7                                       libX11.x86_64 0:1.6.5-2.el7
  libX11-common.noarch 0:1.6.5-2.el7                               libXau.x86_64 0:1.0.8-2.1.el7
  libXcomposite.x86_64 0:0.4.4-4.1.el7                             libXcursor.x86_64 0:1.1.15-1.el7
  libXdamage.x86_64 0:1.1.4-4.1.el7                                libXext.x86_64 0:1.3.3-3.el7
  libXfixes.x86_64 0:5.0.3-1.el7                                   libXft.x86_64 0:2.3.2-2.el7
  libXi.x86_64 0:1.7.9-1.el7                                       libXinerama.x86_64 0:1.1.3-2.1.el7
  libXrandr.x86_64 0:1.5.1-2.el7                                   libXrender.x86_64 0:0.9.10-1.el7
  libXtst.x86_64 0:1.2.3-1.el7                                     libXxf86vm.x86_64 0:1.1.4-1.el7
  libdrm.x86_64 0:2.4.91-3.el7                                     libfontenc.x86_64 0:1.1.3-3.el7
  libglvnd.x86_64 1:1.0.1-0.8.git5baa1e5.el7                       libglvnd-egl.x86_64 1:1.0.1-0.8.git5baa1e5.el7
  libglvnd-glx.x86_64 1:1.0.1-0.8.git5baa1e5.el7                   libjpeg-turbo.x86_64 0:1.2.90-6.el7
  libpciaccess.x86_64 0:0.14-1.el7                                 libthai.x86_64 0:0.1.14-9.el7
  libtiff.x86_64 0:4.0.3-27.el7_3                                  libwayland-client.x86_64 0:1.15.0-1.el7
  libwayland-server.x86_64 0:1.15.0-1.el7                          libxcb.x86_64 0:1.13-1.el7
  libxshmfence.x86_64 0:1.2-1.el7                                  libxslt.x86_64 0:1.1.28-5.el7
  lksctp-tools.x86_64 0:1.0.17-2.el7                               mesa-libEGL.x86_64 0:18.0.5-4.el7_6
  mesa-libGL.x86_64 0:18.0.5-4.el7_6                               mesa-libgbm.x86_64 0:18.0.5-4.el7_6
  mesa-libglapi.x86_64 0:18.0.5-4.el7_6                            pango.x86_64 0:1.42.4-2.el7_6
  pcsc-lite-libs.x86_64 0:1.8.8-8.el7                              pixman.x86_64 0:0.34.0-1.el7
  python-javapackages.noarch 0:3.4.1-11.el7                        python-lxml.x86_64 0:3.2.1-4.el7
  ttmkfdir.x86_64 0:3.0.9-42.el7                                   tzdata-java.noarch 0:2019a-1.el7
  xorg-x11-font-utils.x86_64 1:7.5-21.el7                          xorg-x11-fonts-Type1.noarch 0:7.5-9.el7

Complete!
[centos@ts1 ~]$
```
```
[centos@ts1 ~]$ rpm -qa java*jdk-devel
java-1.8.0-openjdk-devel-1.8.0.212.b04-0.el7_6.x86_64
[centos@ts1 ~]$ javac -version
javac 1.8.0_212
```
# Create user “training” with password “training” and add to group "skcc" for sudo access.
 sudo groupadd skcc

 sudo useradd training -u 3800 -G skcc

 sudo passwd training

 sudo usermod -aG sudo training
 ```
[centos@ts1 ~]$ grep training /etc/passwd
training:x:3800:3800::/home/training:/bin/bash
[centos@ts1 ~]$ grep skcc /etc/group
skcc:x:1001:training
```

# 5. On the host that you will install CM:
## a. Configure the repository for CM 5.15.2
sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
```
[centos@ts1 ~]$ sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
--2019-06-19 05:13:16--  https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo
Resolving archive.cloudera.com (archive.cloudera.com)... 151.101.108.167
Connecting to archive.cloudera.com (archive.cloudera.com)|151.101.108.167|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 290 [binary/octet-stream]
Saving to: ‘/etc/yum.repos.d/cloudera-manager.repo’

100%[=======================================================>] 290         --.-K/s   in 0s

2019-06-19 05:13:16 (69.6 MB/s) - ‘/etc/yum.repos.d/cloudera-manager.repo’ saved [290/290]

[centos@ts1 ~]$
```
sudo vi /etc/yum.repos.d/cloudera-manager.repo
```
baseurl=https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/5.15.2/
```
sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera

## b. Install CM
sudo yum install -y cloudera-manager-daemons cloudera-manager-server
```
  Verifying  : perl-Encode-2.51-7.el7.x86_64                                               19/29
  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                                          20/29
  Verifying  : perl-podlators-2.5.1-3.el7.noarch                                           21/29
  Verifying  : perl-File-Path-2.09-2.el7.noarch                                            22/29
  Verifying  : perl-threads-1.87-4.el7.x86_64                                              23/29
  Verifying  : cloudera-manager-server-5.15.2-1.cm5152.p0.2.el7.x86_64                     24/29
  Verifying  : cloudera-manager-daemons-5.15.2-1.cm5152.p0.2.el7.x86_64                    25/29
  Verifying  : perl-Filter-1.49-3.el7.x86_64                                               26/29
  Verifying  : perl-Getopt-Long-2.40-3.el7.noarch                                          27/29
  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                                      28/29
  Verifying  : perl-Socket-2.010-4.el7.x86_64                                              29/29

Installed:
  cloudera-manager-daemons.x86_64 0:5.15.2-1.cm5152.p0.2.el7
  cloudera-manager-server.x86_64 0:5.15.2-1.cm5152.p0.2.el7

Dependency Installed:
  perl.x86_64 4:5.16.3-294.el7_6                    perl-Carp.noarch 0:1.26-244.el7
  perl-Encode.x86_64 0:2.51-7.el7                   perl-Exporter.noarch 0:5.68-3.el7
  perl-File-Path.noarch 0:2.09-2.el7                perl-File-Temp.noarch 0:0.23.01-3.el7
  perl-Filter.x86_64 0:1.49-3.el7                   perl-Getopt-Long.noarch 0:2.40-3.el7
  perl-HTTP-Tiny.noarch 0:0.033-3.el7               perl-PathTools.x86_64 0:3.40-5.el7
  perl-Pod-Escapes.noarch 1:1.04-294.el7_6          perl-Pod-Perldoc.noarch 0:3.20-4.el7
  perl-Pod-Simple.noarch 1:3.28-4.el7               perl-Pod-Usage.noarch 0:1.63-3.el7
  perl-Scalar-List-Utils.x86_64 0:1.27-248.el7      perl-Socket.x86_64 0:2.010-4.el7
  perl-Storable.x86_64 0:2.45-3.el7                 perl-Text-ParseWords.noarch 0:3.29-4.el7
  perl-Time-HiRes.x86_64 4:1.9725-3.el7             perl-Time-Local.noarch 0:1.2300-2.el7
  perl-constant.noarch 0:1.27-2.el7                 perl-libs.x86_64 4:5.16.3-294.el7_6
  perl-macros.x86_64 4:5.16.3-294.el7_6             perl-parent.noarch 1:0.225-244.el7
  perl-podlators.noarch 0:2.5.1-3.el7               perl-threads.x86_64 0:1.87-4.el7
  perl-threads-shared.x86_64 0:1.43-6.el7

Complete!
```
## c. Install and enable Maria DB (or a DB of your choice)
sudo yum install -y mariadb-server
```
 Installing : perl-DBI-1.627-4.el7.x86_64                                                  9/11
  Installing : perl-DBD-MySQL-4.023-6.el7.x86_64                                           10/11
  Installing : 1:mariadb-server-5.5.60-1.el7_5.x86_64                                      11/11
  Verifying  : 1:mariadb-server-5.5.60-1.el7_5.x86_64                                       1/11
  Verifying  : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                                   2/11
  Verifying  : perl-Net-Daemon-0.48-5.el7.noarch                                            3/11
  Verifying  : perl-Data-Dumper-2.145-3.el7.x86_64                                          4/11
  Verifying  : perl-DBD-MySQL-4.023-6.el7.x86_64                                            5/11
  Verifying  : perl-IO-Compress-2.061-2.el7.noarch                                          6/11
  Verifying  : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                                  7/11
  Verifying  : 1:mariadb-5.5.60-1.el7_5.x86_64                                              8/11
  Verifying  : perl-DBI-1.627-4.el7.x86_64                                                  9/11
  Verifying  : libaio-0.3.109-13.el7.x86_64                                                10/11
  Verifying  : perl-PlRPC-0.2020-14.el7.noarch                                             11/11

Installed:
  mariadb-server.x86_64 1:5.5.60-1.el7_5

Dependency Installed:
  libaio.x86_64 0:0.3.109-13.el7                  mariadb.x86_64 1:5.5.60-1.el7_5
  perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7    perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7
  perl-DBD-MySQL.x86_64 0:4.023-6.el7             perl-DBI.x86_64 0:1.627-4.el7
  perl-Data-Dumper.x86_64 0:2.145-3.el7           perl-IO-Compress.noarch 0:2.061-2.el7
  perl-Net-Daemon.noarch 0:0.48-5.el7             perl-PlRPC.noarch 0:0.2020-14.el7

Complete!
```

sudo systemctl stop mariadb

sudo vi /etc/my.cnf
```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
transaction-isolation = READ-COMMITTED
# Disabling symbolic-links is recommended to prevent assorted security risks;
# to do so, uncomment this line:
symbolic-links = 0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

key_buffer = 16M
key_buffer_size = 32M
max_allowed_packet = 32M
thread_stack = 256K
thread_cache_size = 64
query_cache_limit = 8M
query_cache_size = 64M
query_cache_type = 1

max_connections = 550
#expire_logs_days = 10
#max_binlog_size = 100M

#log_bin should be on a disk with enough free space.
#Replace '/var/lib/mysql/mysql_binary_log' with an appropriate path for your
#system and chown the specified folder to the mysql user.
log_bin=/var/lib/mysql/mysql_binary_log

#In later versions of MariaDB, if you enable the binary log and do not set
#a server_id, MariaDB will not start. The server_id must be unique within
#the replicating group.
server_id=1

binlog_format = mixed

read_buffer_size = 2M
read_rnd_buffer_size = 16M
sort_buffer_size = 8M
join_buffer_size = 8M

# InnoDB settings
innodb_file_per_table = 1
innodb_flush_log_at_trx_commit  = 2
innodb_log_buffer_size = 64M
innodb_buffer_pool_size = 4G
innodb_thread_concurrency = 8
innodb_flush_method = O_DIRECT
innodb_log_file_size = 512M

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
```
sudo systemctl enable mariadb

sudo systemctl start mariadb

sudo /usr/bin/mysql_secure_installation
```
[...]
Enter current password for root (enter for none):
OK, successfully used password, moving on...
[...]
Set root password? [Y/n] Y
New password:
Re-enter new password:
[...]
Remove anonymous users? [Y/n] Y
[...]
Disallow root login remotely? [Y/n] N
[...]
Remove test database and access to it [Y/n] Y
[...]
Reload privilege tables now? [Y/n] Y
[...]
All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```
i. Don’t forget to secure your DB installation
sudo rpm --import https://yum.mariadb.org/RPM-GPG-KEY-MariaDB

## d. Install the mysql connector or mariadb connector
sudo wget "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz"
```
....
-connector-java-5.1.46.tar.gz"
--2019-05-21 10:25:37--  https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
Resolving dev.mysql.com (dev.mysql.com)... 137.254.60.11
Connecting to dev.mysql.com (dev.mysql.com)|137.254.60.11|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz [following]
--2019-05-21 10:25:38--  https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
Resolving cdn.mysql.com (cdn.mysql.com)... 23.212.13.170
Connecting to cdn.mysql.com (cdn.mysql.com)|23.212.13.170|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4434926 (4.2M) [application/x-tar-gz]
Saving to: ‘mysql-connector-java-5.1.46.tar.gz’

100%[======================================>] 4,434,926   --.-K/s   in 0.04s

2019-05-21 10:25:38 (111 MB/s) - ‘mysql-connector-java-5.1.46.tar.gz’ saved [4434926/4434926]
```
tar zxvf mysql-connector-java-5.1.46.tar.gz
```
....
mysql-connector-java-5.1.46/src/testsuite/simple/jdbc42/ConnectionTest.java
mysql-connector-java-5.1.46/src/testsuite/simple/jdbc42/ResultSetTest.java
mysql-connector-java-5.1.46/src/testsuite/simple/jdbc42/StatementsTest.java
mysql-connector-java-5.1.46/src/testsuite/simple/tb2-data.txt.gz
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/ca-cert.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/ca-key.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/ca-truststore
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/certs_howto.txt
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/client-cert.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/client-key.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/client-keystore
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/mykey.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/mykey.pub
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/server-cert.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/server-key.pem
```
sudo mkdir -p /usr/share/java/

## e. Create the necessary users and dbs in your database
i. Grant them the necessary rights

mysql -u root -p

```
drop database if exists amon;
create database amon character set utf8;
grant all privileges on amon.* to amon@'%' identified by 'amon_password';

drop database if exists rman;
create database rman character set utf8;
grant all privileges on rman.* to rman@'%' identified by 'rman_password';

drop database if exists metastore;
create database metastore character set utf8;
grant all privileges on metastore.* to hive@'%' identified by 'hive_password';

drop database if exists sentry;
create database sentry character set utf8;
grant all privileges on sentry.* to sentry@'%' identified by 'sentry_password';

drop database if exists nav;
create database nav character set utf8;
grant all privileges on nav.* to nav@'%' identified by 'nav_password';

drop database if exists navms;
create database navms character set utf8;
grant all privileges on navms.* to navms@'%' identified by 'navms_password';

drop database if exists cdh;
create database cdh character set utf8;
grant all privileges on cdh.* to cdh@'%' identified by 'cdh';

drop database if exists hue;
create database hue character set utf8;
grant all privileges on hue.* to hue@'%' identified by 'hue';

drop database if exists oozie;
create database oozie character set utf8;
grant all privileges on oozie.* to oozie@'%' identified by 'oozie';

drop database if exists hive;
create database hive character set utf8;
grant all privileges on hive.* to hive@'%' identified by 'hive';

drop database if exists scm;
create database scm character set utf8;
grant all privileges on scm.* to scm@'%' identified by 'scm';

exit;
```
## f. Setup the CM database
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm
```
[centos@ts1 ~]$ sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm
Enter SCM password:
JAVA_HOME=/usr/lib/jvm/java-openjdk
Verifying that we can write to /etc/cloudera-scm-server
Creating SCM configuration file in /etc/cloudera-scm-server
Executing:  /usr/lib/jvm/java-openjdk/bin/java -cp /usr/share/java/mysql-connector-java.jar:/usr/share/java/oracle-connector-java.jar:/usr/share/cmf/schema/../lib/* com.cloudera.enterprise.dbutil.DbCommandExecutor /etc/cloudera-scm-server/db.properties com.cloudera.cmf.db.
[                          main] DbCommandExecutor              INFO  Successfully connected to database.
All done, your SCM database is configured correctly!
```
## g. Start the CM server and prepare to install the cluster through the CM GUI installation process
sudo systemctl start cloudera-scm-server
sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log

```
2019-06-19 05:30:47,164 INFO SearchRepositoryManager-0:com.cloudera.server.web.cmf.search.components.SearchRepositoryManager: Finished constructing repo:2019-06-19T05:30:47.164Z
2019-06-19 05:30:47,968 INFO WebServerImpl:org.mortbay.log: jetty-6.1.26.cloudera.4
2019-06-19 05:30:47,968 INFO WebServerImpl:org.mortbay.log: Started SelectChannelConnector@0.0.0.0:7180
2019-06-19 05:30:47,968 INFO WebServerImpl:com.cloudera.server.cmf.WebServerImpl: Started Jetty server.
2019-06-19 05:30:51,502 INFO ScmActive-0:com.cloudera.server.cmf.components.ScmActive: ScmActive completed successfully.
```
