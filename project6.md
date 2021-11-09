# WEB SOLUTION WITH WORDPRESS.
- The task of the project is to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress.
- The project consist of two parts
- i. Configure storage subsystem for Web and Database servers based on Linux OS.
- ii. Install WordPress and connect it to a remote MySQL database server.  
- In project , we will ensure that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

## INSTALLATIONS AND STEPS REQUIRED.
- In this project we wil be using 'Redhat' (fully compatible derivative - CENTOS).
- Create and configure two linux-based virtual servers (EC2 instances in AWS): Webserver and Database.
- Create 3 Volumes for the Webserver and Database instances respectively with availabilty zone same with what is showing in the instances.
![p](https://user-images.githubusercontent.com/50557587/140829333-4f3b8778-7bc4-41f5-83b2-64dd3b3d6654.PNG)
![p](https://user-images.githubusercontent.com/50557587/140829570-58e4b981-b70a-4b0a-9dbd-0f67dd944ab1.PNG)

- Attach volume to the Volumes created for the Webserver and Database ensuring that in the instance slot, you select the instance associated with each instance (Webserver OR Database).
![p1](https://user-images.githubusercontent.com/50557587/140830249-e73937d1-de47-454b-9ea1-7e138530f823.PNG)

- Open the linux terminal to begin configuration, use command `lsblk` to inspect what block devices are attached to both Webserver and Database.
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
- Use lvcreate utility to create 2 logical volumes for Webserver and 1 logical volume for Database respectively.
- For Webserver, the 2 logical volumes are apps-lv (storing data for website) and logs-lv (storing data for logs). Half of the physical volumes will be allocated to each logical volumes.
- The commands are `sudo lvcreate -n apps-lv -L 14G webdata-vg`  and `sudo lvcreate -n logs-lv -L 14G webdata-vg` respectively.
- For Database, only one logical volume is created `sudo lvcreate -n db-lv -L 20G database-vg`.
- Verify logical volume has been created by running sudo lvs.
![p5](https://user-images.githubusercontent.com/50557587/140835274-cd34a9e9-8d7a-43fd-b8d2-40ba1566920d.PNG)

- Format logical volumes with ext4 filesystem for both Webserver and Database
- For Webserver run `sudo mkfs.ext4 /dev/webdata-vg/apps-lv` and `sudo mkfs.ext4 /dev/webdata-vg/logs-lv`, while for Database run `sudo mkfs.ext4 /dev/database-vg/db-lv`.
- In Webserver, create directory to store website files `sudo mkdir -p /var/www/html` and store backup of log data `sudo mkdir -p /home/recovery/logs`.
- In Webserver, mount apps-lv (logical volume) to directory for storing websites files `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`.
- In Webserver, use rsync utility to backup all files in the log directory /var/log into /home/recovery/logs (Required before mounting the file system).
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

![p](https://user-images.githubusercontent.com/50557587/140840641-01b58f12-afa1-4d4f-8147-09f424f1811f.PNG)

- Restart Apache `sudo systemctl restart httpd`.
- Download wordpress and copy content to /var/www/html
- Create wordpress directory and cd into it `mkdir wordpress && cd wordpress`.
- Download wordpress inside wordpress folder `sudo wget http://wordpress.org/latest.tar.gz`, extract the file `sudo tar xzvf latest.tar.gz`.
- Make a copy of wp-config-sample.php as wp-config.php in the same wordpress folder `cp wordpress/wp-config-sample.php wordpress/wp-config.php`.
- cd into wordpress folders `cd wordpress/wordpress/`
- Copy the content inside wordpress into /var/www/html/ `sudo cp -R wordpress/. /var/www/html/`.
- Configure SELinux Policies `sudo chown -R apache:apache /var/www/html`, `sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`, `sudo setsebool -P httpd_can_network_connect=1`.
- Install MySQL in Database Server EC2 `sudo yum update`, `sudo yum install mysql-server`.
- Verify service is up and running `sudo systemctl status mysqld`.
- Run the security script `sudo mysql_secure_installation` which helps to prepare mysql server instance.
- Run MySQL `sudo mysql -u root -p`
- Configure the database to work with WordPress.
![p10](https://user-images.githubusercontent.com/50557587/140843855-0474f121-94e2-4865-ac41-2c464256895a.PNG)

- Edit my.conf.d file in /etc folder to include bind-address




