# Load Balancer Solution With Nginx And SSL/TLS.
- In this project, we will register our website with LetsEnrcypt Certificate Authority to automate certificate issuance.
- The project consist  of two parts: Configuring Nginx as a Load Balancer and registering a new domain name and configure secured connection using SSL/TLS certificates.
- The target architecture will look like this:    
![j11](https://user-images.githubusercontent.com/50557587/143792914-639659bf-e727-42b3-a7d4-2ee57b6e254b.PNG)

## Configure Nginx As A Load Balancer.
- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB  (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections).
- Update /etc/hosts file for local DNS with Web Servers' name and their local IP addressess.    
![j12](https://user-images.githubusercontent.com/50557587/143793106-48bdd8c4-a629-43c2-a7fd-2e39cb696c7d.PNG)

- Install and configure Nginx as a load balancer to point traffic to resolvable DNS names of the webservers `sudo apt update && sudo apt install nginx`.
- Configure Nginx LB using Web Servers' names defined in /etc/hosts `sudo vi /etc/nginx/nginx.conf`.     
![j13](https://user-images.githubusercontent.com/50557587/143793350-02bdd919-720a-4a75-9209-5bd8e985ead8.PNG)

- Restart Nginx and make sure the service is up and running `sudo systemctl restart nginx`, `sudo systemctl status nginx`.

## Register A New Domain Name And Configure Secured Connection Using SSL/TLS Certificates.
- Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other).
- Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP, the purpose of this is to ensure that our public IP address is static because everytime you restart, stop or start your EC2 instance, you get a new public IP address. When you want to associate your domain name, it is better to have a static IP address that does not change after reboot, thus Elastic IP is the solution for this problem.
- Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP address.   
![j5](https://user-images.githubusercontent.com/50557587/143793794-38316a7a-dd85-4f7b-9d63-63f23c9317d7.PNG)

- Associate the Elastic IP with the domain you created by changing the DNS setting of the domain as shown below:   
![j6](https://user-images.githubusercontent.com/50557587/143793874-1f6c11f7-fda2-4ce3-8b8b-14491257b93f.PNG)

- Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol - http://<your-domain-name.com>.        
![j7](https://user-images.githubusercontent.com/50557587/143794016-db5d4faf-4571-4a0f-aabe-6005cc4192c5.PNG)

- Configure your `sudo vi /etc/nginx/nginx.conf` with server_name www.<your-domain-name.com> instead of server_name www.domain.com.  
![j8](https://user-images.githubusercontent.com/50557587/143794096-e4830287-1cf0-44bf-9bc2-979b2e15034e.PNG)

- Install certbot `sudo snap install --classic certbot` and request for an SSL/TLS certificate, make sure snapd service is active and running `sudo systemctl status snapd`.
- Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from `/etc/nginx/nginx.conf`.     
![j9](https://user-images.githubusercontent.com/50557587/143794373-7f89c391-e217-4dc6-834a-fa8aa8c55f93.PNG)

- Install `sudo ln -s /snap/bin/certbot /usr/bin/certbot` and `sudo certbot --nginx`. Test secured access to the Web Solution by trying to reach https://<your-domain-name.com>.   
![j10](https://user-images.githubusercontent.com/50557587/143794639-d97c7a46-da41-4bdb-9fdd-09e0b4f93112.PNG) 

- Set up periodical renewal of your SSL/TLS certificate. By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.
- You can test renewal command in dry-run mode `sudo certbot renew --dry-run`.
- The best practice is to have a scheduled job to run renew command periodically. Add the following command `* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`.
- 






















