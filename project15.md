Create a VPC & enable DNS hostnames.
![image](https://user-images.githubusercontent.com/50557587/150120955-de3f9b6d-34ac-4414-8f22-42833cfc4a7a.png)
![image](https://user-images.githubusercontent.com/50557587/150121031-4880a9ea-64fa-44fd-9d24-3e15195d6df7.png)

Create Internet gateway and attach to the VPC.  
![image](https://user-images.githubusercontent.com/50557587/150121279-bd051110-e67b-4833-b2ca-da99eefb5ea3.png)
![image](https://user-images.githubusercontent.com/50557587/150121381-811623d6-fac4-455e-8853-676851fc31b0.png)

Create Subnet for public and private, in each availablity zone respectively, thus for the public subnet, we create 2 subnet in Availability Zone A and B respectively and for the private subnet we create 4 subnet with respect to the diagram we are working with as such we create 2 subnets each in availability zone A and B each, totally 4 subnets.
![image](https://user-images.githubusercontent.com/50557587/150122385-2774810b-ea4d-4e81-968c-44f5ed1f92e3.png)
![image](https://user-images.githubusercontent.com/50557587/150122421-27723670-0eaf-4114-b121-145f7758e6e7.png)
![image](https://user-images.githubusercontent.com/50557587/150122531-bb0d0afa-2daf-4cb4-9a34-1cd93d7e871e.png)
![image](https://user-images.githubusercontent.com/50557587/150123463-a460fe47-c809-4867-8a98-b51def63b450.png)

Create a route table, private and public each. After creating it, click on the each route table -> Subnet Association -> Edit Subnet Association. For Public route table select the 2 public subnets and for Private route table select the 4 private subnets.   
![image](https://user-images.githubusercontent.com/50557587/150123711-07e4b619-175d-4f3f-8dd7-0eb0f8bf6aa1.png)
![image](https://user-images.githubusercontent.com/50557587/150123986-8e529585-9cfd-4adb-acc3-11e88852ae42.png)
![image](https://user-images.githubusercontent.com/50557587/150124377-4cdbe2f2-492c-4a62-843f-5ce4bf1d1926.png)
![image](https://user-images.githubusercontent.com/50557587/150124519-0c513370-65d3-49c7-8ac9-3674f4548678.png)
![image](https://user-images.githubusercontent.com/50557587/150124578-7b7e1529-0b14-470e-bc8c-4e3f3ccbd2f1.png)
![image](https://user-images.githubusercontent.com/50557587/150124749-eb381064-1023-4dfa-8ef3-69e8b764a9f2.png)

Edit route for each route table. For the Public route table -> add route, the setting is to enable the subnet attached to the route table have access to the internet so the target will be the Internet Gateway i.e communicating with the internet via the internet gateway. 
![image](https://user-images.githubusercontent.com/50557587/150124954-ae085841-6826-4a6c-a6ac-09bd3b4288e8.png)
![image](https://user-images.githubusercontent.com/50557587/150126023-ea56ffed-9d88-4086-849c-815cd7df824e.png)

Create an Elastic IP address that will be used by the NAT-Gateway.  
![image](https://user-images.githubusercontent.com/50557587/150126523-59031509-1d0c-4e0d-b66b-450b25d8d71c.png)

Create the Nat-Gateway, created in a public subnet and attached the elastic ip created.  
![image](https://user-images.githubusercontent.com/50557587/150126982-59d7a676-3713-40b5-ad5b-4f208aeda89c.png)

Go back to the route table and edit Private route table in the edit routes, set target to the Nat-Gateway created earlier.  
![image](https://user-images.githubusercontent.com/50557587/150127348-25aa91c6-754a-497b-8b29-fb090c960a4f.png)

Create security group for:
* External Load balancer - Traffic from the internet (http, https).
* Bastion - SSH access from your computer.
* Nginx (Reverse Proxy Server) - Source of traffic only from the external load balance and SSH from Bastion.
* Internal Load balancer -  traffic only form Nginx.
* Webserver - traffic source from Internal Load Balancer and SSH from Bastion only.
* Datalayer - traffic from Bastion (Mysql), Webserver (NFS and Mysql).    
![image](https://user-images.githubusercontent.com/50557587/150128469-bdddcbf8-f589-4d9f-9707-a0b8c9650ca3.png)
![image](https://user-images.githubusercontent.com/50557587/150129130-cda5ae6c-edc5-4232-92a1-36a1023e06f0.png)
![image](https://user-images.githubusercontent.com/50557587/150134852-9ba280e0-9c8e-460d-99cc-bb95349b3e68.png)
![image](https://user-images.githubusercontent.com/50557587/150135486-ea4c2e62-8f5f-47f3-96cf-2c08456b7aba.png)
![image](https://user-images.githubusercontent.com/50557587/150136559-d18d8173-dfd8-49a1-83c5-8762ed0bd389.png)
![image](https://user-images.githubusercontent.com/50557587/150141661-66347683-fa22-4463-be6b-be4ac3a7a2db.png)
![image](https://user-images.githubusercontent.com/50557587/150141832-3b711557-d388-4c41-8642-2625a62cbe80.png)

Create certificate, ensure domain purchased is transferred to AWS Route 53. The purpose of the certificate is mainly because of the load balancer which will need a certificate because it listens to traffic from port 443. Ensure to create record on Route 53 after creating the certificate.
![image](https://user-images.githubusercontent.com/50557587/150143152-d06bc918-c9ec-487a-b06f-c8b7985b6859.png)
![image](https://user-images.githubusercontent.com/50557587/150143710-6a50797b-503a-49c0-807e-901aac13d93f.png)

Create Amazon EFS: Add mount target to the filesystem i.e specifying subnet. Wherever you specify your mount target the Amazon EFS becomes avaible in that subnet as such we specify it in private subnet 1 & 2 and set security group setting to datalayer security we created.      
![image](https://user-images.githubusercontent.com/50557587/150144572-24ac0645-a57a-4c14-82b1-1e21a4c3c59a.png)       
![image](https://user-images.githubusercontent.com/50557587/150146070-011fac1e-9617-4a8c-969e-052693b096bf.png)

Create access point next which will specify where the webservers will mount with, thus creating 2 mount points for Tooling and Wordpress servers each.       
![image](https://user-images.githubusercontent.com/50557587/150147463-bd4a62ba-6a19-44b1-a57b-cd896c6ba403.png)
![image](https://user-images.githubusercontent.com/50557587/150338385-0fe61ebb-af9f-4c1d-b4ef-0c49cac1a76a.png)   
![image](https://user-images.githubusercontent.com/50557587/150148559-419b7ae8-44e3-4cef-826d-107ab86e2cff.png)

Create Amazon RDS, before we commence on that we need to create KMS key (to encrypt RDS instance) and subnet group (placed in private subnet)
![image](https://user-images.githubusercontent.com/50557587/150149517-9d308809-9f54-4a60-afa3-a3f8fa6c0bf0.png)
![image](https://user-images.githubusercontent.com/50557587/150149821-798e0478-d061-4ca0-aa65-c8d842f99733.png)
![image](https://user-images.githubusercontent.com/50557587/150339816-db65fae4-46e0-4d57-8788-99cc8294a6c4.png)
![image](https://user-images.githubusercontent.com/50557587/150340022-f0f5e8c0-4a76-4b26-af1f-a391eb7089ba.png)
![image](https://user-images.githubusercontent.com/50557587/150167610-6e078e4f-65a6-4efd-b003-b71cde1b4882.png)
![image](https://user-images.githubusercontent.com/50557587/150168567-8b6cdcfc-1759-4122-9a33-bb2fd21fde12.png)
![image](https://user-images.githubusercontent.com/50557587/150169001-f2d1844e-da3f-4ae1-bb6d-e12906eb4a02.png)
![image](https://user-images.githubusercontent.com/50557587/150169138-f7a6ea7d-716b-4370-b94b-9b30bc030596.png)

Create database   
![image](https://user-images.githubusercontent.com/50557587/150170525-f646b2b0-8ed9-4612-a0fe-5533f9a346cf.png)
![image](https://user-images.githubusercontent.com/50557587/150170641-246f331e-f438-4401-a42a-dc19b4df8baf.png)
![image](https://user-images.githubusercontent.com/50557587/150170744-3eefb5da-e6af-4ee2-b419-9ad28748e66b.png)
![image](https://user-images.githubusercontent.com/50557587/150170847-1563e4d3-ff3d-40c0-9983-5874e011d038.png)
![image](https://user-images.githubusercontent.com/50557587/150170997-f8067b22-4458-4c11-90f1-63ab62a4dad6.png)

Create Autoscaling group.

Create 3 Instances (Redhat) with security group (All traffic - anywhere) named Bastion, Nginx and Webserver.

Launch each instance and do some installation configurations

For Bastion, Nginx and Webserver
```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
yum install wget vim python3 telnet htop git mysql net-tools chrony -y
systemctl start chronyd
systemctl enable chronyd
```

Nginx and Webserver (needed so that our servers can function properly on all the redhat instance)
```
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1
```

Install Amazon efs utils for mounting targets on the elastic file system (Nginx)
```
git clone https://github.com/aws/efs-utils
cd efs-utils
yum install -y make
yum install -y rpm-build
make rpm 
yum install -y  ./build/amazon-efs-utils*rpm
````

Install self signed certificate for the webservers (Nginx)
```
sudo mkdir /etc/ssl/private
sudo chmod 700 /etc/ssl/private
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ACS.key -out /etc/ssl/certs/ACS.crt
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```
Succesfully generating the certificate. In the common name, you enter  the private IPv4  dns of the instance (Webserver/Nginx). The load balancer does not validate the certicates but it needs to be installed. We use the certificate by specifying the path to the file ACS.crt and ACS.key in the nginx configuration. 
![image](https://user-images.githubusercontent.com/50557587/150351723-d7ab1e28-8f7b-4b55-92e1-b9f6ca64e02e.png)
![image](https://user-images.githubusercontent.com/50557587/150647078-cc1777e8-032c-4b02-8672-fe54e3e2ac1e.png)

To confirm if the ACS.crt and ACS.key file exist
![image](https://user-images.githubusercontent.com/50557587/150353343-ac98b20c-455b-4435-a106-1dfd3148dfa3.png)

Install self signed certificate for the webservers (Apache)
```
yum install -y mod_ssl
openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/ACS.key -x509 -days 365 -out /etc/pki/tls/certs/ACS.crt
vi /etc/httpd/conf.d/ssl.conf
```
Edit the ssl.conf to conform with the key and crt file we created.
![image](https://user-images.githubusercontent.com/50557587/150358614-fe8c03f6-1b9f-41da-83db-c51d875187d8.png)


For each of the instance created, we create AMI on each.  
![image](https://user-images.githubusercontent.com/50557587/150359106-824b4336-be22-4c3b-a2d2-a7b981480b31.png)
![image](https://user-images.githubusercontent.com/50557587/150359346-e6a8e847-3613-4fcd-9459-b101bcff8046.png)

Create Target groups for Nginx, Worpress and Tooling because they are all behind a load balancer. The Auto-Scaling will launch instance in this target group.
![image](https://user-images.githubusercontent.com/50557587/150360649-54529cd5-0325-41fc-95fa-19b2fbb5d618.png)
![image](https://user-images.githubusercontent.com/50557587/150360778-fc602208-9c08-45cf-baed-eaef01e3b7f1.png)

Create Load Balancers (External Load Balancer - Internet facing). A load balancer needs at least 2 availabilty zones to work and placed in a public subnet.   
![image](https://user-images.githubusercontent.com/50557587/150383384-81c3f79c-1c1b-4197-b2d5-1ee05f57966f.png)
![image](https://user-images.githubusercontent.com/50557587/150383582-41c8c716-a1b2-48d9-85e6-197d3496f8b1.png)
![image](https://user-images.githubusercontent.com/50557587/150383696-a73e2b9f-0520-4e1c-b761-04194234fad0.png)
![image](https://user-images.githubusercontent.com/50557587/150383769-65ce600e-60a1-42f4-933b-48a3a632b662.png)
![image](https://user-images.githubusercontent.com/50557587/150384013-c1ae7e68-e560-4506-92f1-24b61e51b421.png)

Create Load Balancers (Internal Load Balancer - Internal facing) to be placed in private subnet.      
![image](https://user-images.githubusercontent.com/50557587/150384659-1ed2c6bc-b822-4a99-a1ac-8cb4cb999746.png)
![image](https://user-images.githubusercontent.com/50557587/150384749-5bcbd443-8cd6-4388-808d-10554c1ccf8b.png)
![image](https://user-images.githubusercontent.com/50557587/150384879-46ac493b-fe20-4246-ae15-1ac390aabe7a.png)
![image](https://user-images.githubusercontent.com/50557587/150384984-54ce230b-a9d6-4b83-b92c-cd3808dbb1d3.png)


Select internal load balancer and click on listeners, from the screenshot, it could be depicted that every traffic that comes into the load balancer forwards to Wordpress target.
![image](https://user-images.githubusercontent.com/50557587/150385555-c58d8a53-9767-4944-a8ca-248f7973a269.png)

We can configure a rule so that we can cache tooling request and forward to the tooling target
![image](https://user-images.githubusercontent.com/50557587/150386415-c7120a4c-9dc6-4874-b781-a4e3003219ac.png)
![image](https://user-images.githubusercontent.com/50557587/150386929-ae4b7310-4b2a-42ad-b3cb-acc6d80ef3c7.png)

Create launch templates (Bastion, Nginx, Wordpress and Tooling).
Wordpress and Tooling both make use of Webserver AMI
![image](https://user-images.githubusercontent.com/50557587/150390913-b00794e2-f633-47bc-9a9a-9a587b1d4d26.png)
![image](https://user-images.githubusercontent.com/50557587/150392726-19175cec-bb25-43e8-9f3b-14dd496fe978.png)
![image](https://user-images.githubusercontent.com/50557587/150392902-7abfad14-faf7-4a69-9e40-49a4b2338881.png)
![image](https://user-images.githubusercontent.com/50557587/150392968-d16e946d-f962-4d1d-9e53-7a2561106152.png)

Apply the same setting for Nginx, the major difference is the userdata input  
![image](https://user-images.githubusercontent.com/50557587/150422434-c2f40b05-d048-4621-a3cc-810bcfccf8b8.png)

Wordpress userdata
```
#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0cb279f5ee79c59f4 fs-0501ac1736dff39ea:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
mkdir /var/www/html/
cp -R /wordpress/* /var/www/html/
cd /var/www/html/
touch healthstatus
sed -i "s/localhost/acs-database.cbzlhilzcstn.us-east-2.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/ACSADMIN/g" wp-config.php 
sed -i "s/password_here/password/g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd
```

We have to update the reverse.conf file by updating the end point of the internal load balancer (DNS name) in the proxy_pass section of the file.  
![image](https://user-images.githubusercontent.com/50557587/150648675-8d9df05f-833c-4af0-8564-ede28760e53b.png)

Update the mount point to the file system
`sudo mount -t efs -o tls,accesspoint=fsap-0cb279f5ee79c59f4 fs-0501ac1736dff39ea:/ /var/www/`  
![image](https://user-images.githubusercontent.com/50557587/150426608-2b90d1fc-62d7-44da-b724-88121304f7c1.png)
![image](https://user-images.githubusercontent.com/50557587/150427269-dd082338-1c79-4d3e-bc82-958f59669ee9.png)


word press endpoint
![image](https://user-images.githubusercontent.com/50557587/150510954-4dc182a0-83c3-4beb-ac65-1615a45288da.png)

Paste the rds end-point in the wordpress userdata and tooling userdata 
![image](https://user-images.githubusercontent.com/50557587/150511435-cb7a412a-ee8a-4a25-afb8-29afa34aa29d.png)

Create Autoscaling Group for Bastion, Nginx, WordPress and Tooling 
The same setting used for Bastion will be used for Nginx, the only difference is Load Balancer is added to Nginx
![image](https://user-images.githubusercontent.com/50557587/150515542-c5276d57-4621-4b41-b646-a91c2c12e182.png)
![image](https://user-images.githubusercontent.com/50557587/150516194-1eb8fb61-237b-4a19-ac12-5d0eeac5ff67.png)
![image](https://user-images.githubusercontent.com/50557587/150516334-d95ac6c9-0ccf-410e-828c-4aba93d4ce3c.png)
![image](https://user-images.githubusercontent.com/50557587/150516526-b7763799-2548-4a4f-9a08-103be95028b1.png)
![image](https://user-images.githubusercontent.com/50557587/150516858-b3df64c6-7519-4122-9cec-e6360530e285.png)


Copy the endpoint of the database. 
![image](https://user-images.githubusercontent.com/50557587/150548054-9299c62e-2f1d-4788-ae50-0bd26a2fa971.png)

Create database for tooling and wordpress for Bastion
![image](https://user-images.githubusercontent.com/50557587/150548725-faae3ac6-625b-4d96-86b7-8624c5770659.png)



![image](https://user-images.githubusercontent.com/50557587/150561500-90f776ea-c484-4ceb-b0e3-2adca4e1eb99.png)

Create record for our load balancer (route 53)
![image](https://user-images.githubusercontent.com/50557587/150565164-f2916f80-dea4-45ee-b1bf-338906c88f3f.png)
![image](https://user-images.githubusercontent.com/50557587/150566028-f51773b8-b692-45ac-8570-665e3bdfd2e0.png)
![image](https://user-images.githubusercontent.com/50557587/150566101-df3172ce-7570-4a0e-964e-65bee3efb9cb.png)
![image](https://user-images.githubusercontent.com/50557587/150566312-7f982d21-c15b-48c4-b48f-9937eec345b1.png)

e

Alltarget group are healthy.  
![image](https://user-images.githubusercontent.com/50557587/150592222-0882c0c9-8213-49ce-b60a-2cad4e333456.png)
![image](https://user-images.githubusercontent.com/50557587/150592292-30dfc677-b3fe-4702-8739-85fae4faeaee.png)
![image](https://user-images.githubusercontent.com/50557587/150592332-e3a19b2a-727f-4d5a-9907-955b7a1c560d.png)
![image](https://user-images.githubusercontent.com/50557587/150592507-24554c2c-89f4-4f2b-b0a5-abda3cf911c5.png)


![image](https://user-images.githubusercontent.com/50557587/150600292-35d07bda-9c60-4808-a423-b3da31a0acd8.png)






















































































