# CLIENT-SERVER ARCHITECTURE WITH MYSQL.

The task of the project is to implement a Client Server Architecture using MySQL Database Management System (DBMS).

- Create and configure two linux-based virtual servers (EC2 instances in AWS) and connect.
- Server A name - 'MYSQL server', Server B name - 'MYSQL client'. 
- On MYSQL server install MYSQL Server software `sudo apt update -y` `sudo apt install mysql-server -y`.
- Enable the service `sudo systemctl enable mysql`.
- On MYSQL client install MySQL Client software `sudo apt update -y` `sudo apt install mysql-client -y`.
- Update the inbound rules in MYSQL server, by adding TCP port 3306. For extra security allow access only to private IP address of MYSQL client.
![mysql1](https://user-images.githubusercontent.com/50557587/140239182-b569196a-41f4-4eee-a45f-c9953f9caf20.PNG)

- In MYSQL server, run the security script  `sudo mysql_secure_installation` which helps to prepare mysql server instance.
- Run mysql `sudo mysql`.
- Create  a remote user, database, grant all privileges on the database and flush privileges. See screenshot below.
![mysql2](https://user-images.githubusercontent.com/50557587/140240066-eb62e221-3362-4a43-90f5-aa536e6c4513.PNG)

- Exit mysql.
- User and database all created.
- Configure MYSQL server to allow connections from remote hosts `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`.
- Replace ‘127.0.0.1’ to ‘0.0.0.0’ in the bind-address
![mysql3](https://user-images.githubusercontent.com/50557587/140240650-7ad06901-d4c3-4422-a52d-6e312e64e959.PNG)

- Restart mysql `sudo systemctl restart mysql`.
- From MYSQL client connect remotely to MYSQL server `sudo mysql -u remote_user -h 172.31.13.73 -p`.
- Now connected from our client instance.
- Confirm successfully connection to the remote MySQL server and perform SQL queries: `Show database;`
- The screenshot below shows a fully functional MYSQL Client-Server set up.
![mysql4](https://user-images.githubusercontent.com/50557587/140241404-c36fc1ab-8401-4a47-b417-67f8eda09c02.PNG)
 

