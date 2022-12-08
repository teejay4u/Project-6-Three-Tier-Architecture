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

15. 