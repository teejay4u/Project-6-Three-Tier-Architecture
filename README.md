# Project-6-Three-Tier-Architecture
>># **Web Solution with wordpress**

### 1. Project Description 
    This project consists of two parts
    1. The first part was to configure a storage subsystem for web and database servers based on Linux OS (Redhat). The volumes were partitioned accordingly using gdisk.    
    2. The second part was to install wordpress and connect it to a remote MySQL database server.

### 2. The set-up was done using AWS-Virtual machines, 2 VMs was provisioned, 1 for the database and 1 for the web server. A pc was then used for the client.
    The Setup 
    I. A Laptop to serve as client 
    II. An Ec2 linux server for the web-server (To install wordpress)
    III. An EC2 linux server (Database server)

>## Project in Details

### 3. Server Configuration
    I. Launch EC2 machine and attach 3 volumes of 10gb in the same AZ
    II. Used lsblk command to list the available storage attached to the VM
    III. used df -h command to see all mount points and free space on the volumess created
4. **ls/dev command output**
![](/Displaying%20volumes%20available%20on%20webserver.png)

5. **lsblk command output**
![](/2using%20lsblk%20command%20to%20display%20volumes%20available.png)

### 6. Using gdisk utitlity to create a single partition on each of the 3 volumes created and pvcreate to create physical volumes
    I. sudo gdisk /dev/xvdb
    II. sudo gdisk /dev/xvdc
    III. sudo gdisk /dev/xvdf
    IV. sudo pvcreate /dev/xvdb 
    V. sudo pvcreate /dev/xvdc 
    VI. sudo pvcreate /dev/xvdf

6. using **lsblk** to see created partitions and **pvcreate** to create physical volumes to be used by the LVM
![](adding%20physical%20volumes%20to%20VG%20with%20the%20name%20webdata-vg.png)

7. created a volume group with **sudo vgcreate** utility and named it as webdata-vg **(sudo vgcreate webdata-vg /dev/xvdb /dev/xvdc /dev/xvdf)**
![](8.adding%20physical%20volumes%20to%20VG%20with%20the%20name%20webdata-vg.png)

8. used the **sudo lvcreate** utility to create logical volumes **(sudo lvcreate -n apps-lv -L 14GB webdata-vg)**, **(sudo lvcreate -n logs-lv -L 14GB webdata-vg)**
![](10.%20creating%20logical%20volumes%20from%20volume%20group%20for%20apps%20data%20and%20log%20data.png)

9. verified the setup by running **(sudo lsblk)**

![](11.%20verifying%20setup%20of%20volumes%20sudo%20lvs.png)

10. used ext4 file system to format the logical volumes **(sudo mkfs -t ext4 /dev/webdata-vg/apps-lv)** 
![](12.%20formatting%20the%20logical%20volumes%20with%20ext4.png)

11. created directories to store website files and log data

![](13.creating%20directories%20to%20store%20website%20and%20log%20files.png)

12. Mounting directory for logical volume 

![](14.%20mounting%20website%20directory%20on%20apps%20logical%20volume.png)

13. updating fstab file in **/etc/fstab** so mount configurations will persist after server restarts 

![](19.%20updating%20fstab%20with%20mount%20point%20paths.png)

14. checking to make sure the setup is up and running 

![](20.%20checking%20setup%20is%20running%20df%20-h.png)

15. configuring DB-Instance(creting partitions) 

![](db-configuring-partitions.png)

16. Verifying DB-setup

![](/verifying%20the%20setup.png)

17. Installing wordpress on webserver 
    I. updated the repository with **sudo yum update -y** 
    ii. installed wget, apache and its dependencies **(sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json)**
![](/21.%20installing%20apache%20dependencies%20and%20updating%20db-server.png)
    iii. istalled php and its dependencies 

![](21.%20installing%20apache%20dependencies%20and%20updating%20db-server.png)
    iv. Downloaded wordpress and copied its files to the /var/www/html/ directory 
![](23.%20downloading%20wordpess%2C%20unzipping%20and%20copying%20defualt%20page%20to%20app%20directory.png)

18. Configuring SELinux policies
    sudo chown -R apache:apache /var/www/html/wordpress
![](25.%20giving%20the%20apache%20service%20ownership%20of%20website%20directory.png)
    sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
    sudo setsebool -P httpd_can_network_connect=1
![](26.%20Setting%20SElinux%20policy%20on%20website%20directory.png)

19. installing MySQL on the db-server
    sudo yum update
    sudo yum install mysql-server
![](27.%20updating%20and%20installing%20mysql-server.png)

20. checking the mysql service is running by running **(sudo systemctl status mysqld)**
![](/28.%20checking%20mysqld%20service%20is%20running%20.png)

21. Confguring Database to work with wordpress 
    sudo mysql
    CREATE DATABASE wordpress;
    CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
![](29.%20creating%20user%20for%20database%20and%20setting%20password.png)
    GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
    FLUSH PRIVILEGES;
![](30.%20creating%20user%20for%20db%20and%20setting_granting%20permissions%20to%20the%20user.png)
    SHOW DATABASES;
    exit

22. successfully connected to Db server from webserver

![](/successfully%20connected%20to%20db-server%20from%20web-server.png)

23. Wordpress Welcome Page 

![](/Wordpress%20installed.png)
