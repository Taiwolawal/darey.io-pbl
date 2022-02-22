# WEB SOLUTION WITH WORDPRESS.
- The task of the project is to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress.
- The project consist of two parts
- i. Configure storage subsystem for Web and Database servers based on Linux OS.
- ii. Install WordPress and connect it to a remote MySQL database server.  
- In this project , we will ensure that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

## INSTALLATIONS AND STEPS REQUIRED.
- In this project we wil be using 'Redhat' (fully compatible derivative - CENTOS).
- Create and configure two linux-based virtual servers (EC2 instances in AWS): Webserver and Database.
- Create 3 Volumes for the Webserver and Database instances respectively with availabilty zone same with what is showing in the instances.
![p](https://user-images.githubusercontent.com/50557587/140829333-4f3b8778-7bc4-41f5-83b2-64dd3b3d6654.PNG)
![p](https://user-images.githubusercontent.com/50557587/140829570-58e4b981-b70a-4b0a-9dbd-0f67dd944ab1.PNG)

- Attach volume to the Volumes created for the Webserver EC2 and Database EC2 ensuring that in the instance slot, you select the instance associated with each instance (Webserver OR Database).
![p1](https://user-images.githubusercontent.com/50557587/140830249-e73937d1-de47-454b-9ea1-7e138530f823.PNG)

- Open the linux terminal to begin configuration, use command `lsblk` to inspect what block devices are attached to both Webserver and Database EC2.
- Use gdisk to create a single partition on each of the 3 disks `sudo gdisk /dev/xvdf /dev/xvdg /dev/xvdh`.
- Use lsblk utility to view the newly configured partition on each of the 3 disks. Check screenshot below.
![p2](https://user-images.githubusercontent.com/50557587/140831068-02f5b866-c270-4883-91e0-e6402ac515cb.PNG) 

- Install lvm2 package using `sudo yum install lvm2 -y` and run `sudo lvmdiskscan` to check available partition.
![p3](https://user-images.githubusercontent.com/50557587/140831944-f41306d8-a18b-4d78-afa8-4beb0e5522ec.PNG)


- Run pvcreate on each of the 3 disks as physical volumes to used by LVM `sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`.
- Verify phyical volume has been created successfully running `sudo pvs`.
![p4](https://user-images.githubusercontent.com/50557587/140832000-fb61701c-858a-48a4-83e8-d228bc812694.PNG)

- Run vgcreate command to add all 3 physical volumes to a volume group (VG), namely webdata-VG and database-VG respectively for both instances. 
- The commands are `sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`, `sudo vgcreate database-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1` respectively.
- Verify that volume group has been created successfully by running `sudo vgs`.
- Use lvcreate utility to create 2 logical volumes for Webserver EC2 and 1 logical volume for Database EC2 respectively.
- For Webserver EC2, the 2 logical volumes are apps-lv (storing data for website) and logs-lv (storing data for logs). Half of the physical volumes will be allocated to each logical volumes.
- The commands are `sudo lvcreate -n apps-lv -L 14G webdata-vg`  and `sudo lvcreate -n logs-lv -L 14G webdata-vg` respectively.
- For Database EC2, only one logical volume is created `sudo lvcreate -n db-lv -L 20G database-vg`.
- Verify logical volume has been created by running sudo lvs.
![p5](https://user-images.githubusercontent.com/50557587/140835274-cd34a9e9-8d7a-43fd-b8d2-40ba1566920d.PNG)

- Format logical volumes with ext4 filesystem for both Webserver and Database EC2.
- For Webserver EC2 run `sudo mkfs.ext4 /dev/webdata-vg/apps-lv` and `sudo mkfs.ext4 /dev/webdata-vg/logs-lv`, while for Database run `sudo mkfs.ext4 /dev/database-vg/db-lv`.
- In Webserver EC2, create directory to store website files `sudo mkdir -p /var/www/html` and store backup of log data `sudo mkdir -p /home/recovery/logs`.
- In Webserver EC2, mount apps-lv (logical volume) to directory for storing websites files `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`.
- In Webserver EC2, use rsync utility to backup all files in the log directory /var/log into /home/recovery/logs (Required before mounting the file system).
- Run command `sudo rsync -av /var/log/. /home/recovery/logs/`.
- Mount logs-lv (logical volume) to directory /var/log, `sudo mount /dev/webdata-vg/logs-lv /var/log`.
- All existing data in the /var/log will be deleted when logs-lv (logical volume) is mounted as such that is why created backup file using the rsync utilty for all the contents in the folder.
- Restore log files back into /var/log directory `sudo rsync -av /home/recovery/logs/. /var/log`.
- Update /etc/fstab file to ensure the mount configuration will persist after restart of the server.
- Run sudo blkid to copy the UUID of the device for both Webserver and Database 
![p7](https://user-images.githubusercontent.com/50557587/140838828-c69881f7-00ac-43c1-813f-a26aad1746ae.PNG)

- Run sudo vi /etc/fstab to update the content with the UUID information.
![p8](https://user-images.githubusercontent.com/50557587/140839162-ebd7e311-0997-45c8-9947-02d72da3717f.PNG)

- Test the configuration `sudo mount -a`, if no error message displays then it implies it installations went smoothly.
- Reload the daemon `sudo systemctl daemon-reload`.
- Verify your setup by running `df -h`.

![p9](https://user-images.githubusercontent.com/50557587/140840066-c69a5bd8-ee7d-4530-8de9-975d6deb9da8.PNG) 

- Install WordPress, Apache and its dependecies on Webserver EC2 `sudo yum -y update` , `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`.
- Start Apache sudo `systemctl enable httpd` , `sudo systemctl start httpd`.
- Install PHP and its dependencies.
```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

- Restart Apache `sudo systemctl restart httpd`.
- Download wordpress and copy content to /var/www/html
- Create wordpress directory and cd into it `mkdir wordpress && cd wordpress`.
- Download wordpress inside wordpress folder `sudo wget http://wordpress.org/latest.tar.gz`, extract the file `sudo tar xzvf latest.tar.gz`.
- Make a copy of wp-config-sample.php as wp-config.php in the same wordpress folder `cp wordpress/wp-config-sample.php wordpress/wp-config.php`.
- cd into wordpress folders `cd wordpress/wordpress/`
- Copy the content inside wordpress into /var/www/html/ `sudo cp -R wordpress/. /var/www/html/`.
- Configure SELinux Policies in the Webserver SE2 `sudo chown -R apache:apache /var/www/html`, `sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`, `sudo setsebool -P httpd_can_network_connect=1`.
- Install MySQL in Database Server EC2 `sudo yum update`, `sudo yum install mysql-server`.
- Verify service is up and running `sudo systemctl status mysqld`.
- Run the security script `sudo mysql_secure_installation` which helps to prepare mysql server instance.
- Run MySQL `sudo mysql -u root -p`
- Configure the database to work with WordPress.
![p10](https://user-images.githubusercontent.com/50557587/140843855-0474f121-94e2-4865-ac41-2c464256895a.PNG)

- In the Database Server EC2 instance, edit my.cnf.d file in /etc folder to include bind-address 0.0.0.0 sudo vi /etc/my.cnf.d and restart mysql `sudo systemctl restart mysqld`.
![p11](https://user-images.githubusercontent.com/50557587/140844961-3aa18100-3b86-45b5-9680-9664e85ac166.PNG)

- In the Webserver EC2 instance, edit wp-config.php in /var/www/html/ folder to include Database Server MySQL information `sudo vi wp-config.php`.

![p12](https://user-images.githubusercontent.com/50557587/140845911-1fe8bc52-43f1-4ad0-b5dc-d9a84967f53a.PNG)

- Restart httpd `sudo systemctl restart http`.
- Disable the default page of Apache `mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup`.
- Open MYSQL port 3306 on the Database Server EC2, for extra security allow access to the Database server ONLY from the WebServer's IP address.
![p](https://user-images.githubusercontent.com/50557587/140846896-1bc569d2-4a0d-4040-9b28-6930925705e8.PNG)

- To confirm if Webserver EC2 can communicate with Database EC2, run `sudo mysql -h 172.31.4.234 -u wordpress -p`.
![p13](https://user-images.githubusercontent.com/50557587/140847063-8284d8cc-ff00-4309-bae2-1d5baa124e58.PNG)

-  Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP).
-  Try to access from the browser the link to your WordPress
![p15](https://user-images.githubusercontent.com/50557587/140848108-6bc32df7-9a3c-4e7d-ab6d-ff3b720a7a2b.PNG)
![p16](https://user-images.githubusercontent.com/50557587/140848147-0f5da57e-49a5-4ed8-a3b7-dec774bd7c7b.PNG)




