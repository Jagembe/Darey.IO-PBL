## The following prerequisites were in place for the project (Part of Project 7):
  1. Two RHEL8 Web Servers
  2. One MySQL DB Server (based on Ubuntu 20.04)
  3. One RHEL8 NFS Server

## The following configuration was pre-established prior to embarking on this project:
  - Apache (httpd) process is up and running on both Web Servers
  - '/var/www' directories of both Web Servers
  - All necessary TCP/UDP ports are open on the Web, DB and NFS Servers
  - Client browser can access both Web Servers by their respective Public IP addresses or Public DNS names and can open the Tooling Website (e.g. http://<Public-IP-Address-or Public-DNS-Name>/index.php)
  
  The following is the pre-requisite architecture for **Project 8**:
  
  ![image](https://user-images.githubusercontent.com/58337007/154568673-e566569a-9437-405d-8e7e-c8000150a19e.png)
  
  ## Configuring Apache as a Load Balancer:
  
  1. Created an Ubuntu Server 20.04 EC2 instance and named it **Project-8-apache-lb**:
  
  ![image](https://user-images.githubusercontent.com/58337007/154781048-eac9a35d-fc44-4ff4-8b7d-fb87cd7c9945.png)

  2. Opened TCP port 80 on 'Project-8-apache-lb' by creating an Inbound Rule in Security Group.
  
  ![image](https://user-images.githubusercontent.com/58337007/154781175-98721504-aa6d-4f24-a537-b31f40ca1b5e.png)
  
  3. Installed Apache Load Balancer on Project-8-apache-lb server and configured it to point traffic coming to LB to both Web Servers by running the following cmdlets:
  
  ```
  # Install apache2
  sudo apt update
  sudo apt install apache2 -y
  sudo apt-get install libxml2-dev

  # Enable following modules:
  sudo a2enmod rewrite
  sudo a2enmod proxy
  sudo a2enmod proxy_balancer
  sudo a2enmod proxy_http
  sudo a2enmod headers
  sudo a2enmod lbmethod_bytraffic

  # Restart apache2 service
  sudo systemctl restart apache2
  ```
  
  Made sure apache2 service was up and running by using the cmdlet below:
  
  `sudo systemctl restart apache2`
  
  ![image](https://user-images.githubusercontent.com/58337007/154783226-42b822ed-0e98-4b8e-84dd-2539bbe013af.png)
  
  Configured load balancing by by running the cmdlet below:
  
```
sudo vi /etc/apache2/sites-available/000-default.conf

#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

#Restart apache server

sudo systemctl restart apache2

```

4. Verified that our configuration works by accessing the LB's public IP address or Public DNS name from my browser by running the cmdlet below.
  
  `http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`
  
  ![image](https://user-images.githubusercontent.com/58337007/154786208-e02ed31f-4a87-4b3d-ae6b-44a899824057.png)
  
  ![image](https://user-images.githubusercontent.com/58337007/154786376-997c5cf4-98c7-41b7-958f-6a037b4b433b.png)

  Unmounted the '/var/log/httpd/ from the Web Servers to the NFS server and made sure that each Web Server has its own log directory using the cmdlet below:
  
  `sudo umount -f /var/log/httpd`
  
  `sudo tail -f /var/log/httpd/access_log`
  
  ![image](https://user-images.githubusercontent.com/58337007/154786843-cd70484c-a5b5-461a-a653-ecbe125da279.png)
  
  Refreshed the browser page several times to make sure that http://<35.173.191.150/index.php server receive HTTP GET requests from the LB - new records appeared in each servers log file. 
  
                                                                                              
 The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers – it means that traffic will be disctributed evenly between them.                                                                                             
  
  ![image](https://user-images.githubusercontent.com/58337007/154787005-b1e0d210-8ea5-43b3-bbb4-99c31e6f0101.png)
  
                                                                                             
                                                                                              
                                                                                              
  
  
  
  
  
  
  
 
