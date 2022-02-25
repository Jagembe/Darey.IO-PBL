# CONFIGURED NGINX AS A LOAD BALANCER
1. Created a fresh installation of Linux for NGINX (EC2 VM based on Ubuntu Server 20.04 LTS and named it Nginx LB, opened TCP port 80 for HTTP connections and TCP pport 443 ofr secured HTTPS conections.
![image](https://user-images.githubusercontent.com/58337007/155448141-9d4e2558-6872-419d-a78e-bdbe132bf2ef.png)
 
2. Registered domain name 'toolingen.me' and created a Route 53 hosted zone.
![image](https://user-images.githubusercontent.com/58337007/155449347-d5ef8493-466f-4b0a-bdff-0d6a470edce4.png)
 
3. Updated the DNS Server names in the Domain record name with the values from the AWS Route 53.
![image](https://user-images.githubusercontent.com/58337007/155450060-d0143234-fe10-420b-b69a-0f6525f707b1.png)

4. Created a record in AWS Route 53 to point to the Load Balancer public IP. 
![image](https://user-images.githubusercontent.com/58337007/155454976-25d39af3-65a0-4f01-9e6f-04aca3e68723.png)
![image](https://user-images.githubusercontent.com/58337007/155455248-1cf96445-6114-4b27-a32c-51b9231e09e2.png)

5. SSH'd into the NGINX Load Balancer Server ran the following cmdlet:
  ```
  sudo apt update && sudo apt install nginx -y
  ```
![image](https://user-images.githubusercontent.com/58337007/155455967-34feda44-7e6a-484d-ae04-0e79250a6759.png)

6. Enabled nginx and started it using the following cmdlet:
 `sudo systemctl enable nginx && sudo systemctl start nginx`
 ![image](https://user-images.githubusercontent.com/58337007/155456347-491cbf45-0a5b-460e-9352-2a0197d8e29b.png)

7. Checked to see if nginx has been successfully installed using the following cmdlet:
 `sudo systemctl status nginx`
 ![image](https://user-images.githubusercontent.com/58337007/155457063-b4530d54-003c-45fe-8102-f6c918c687ca.png)
 
 8. Created a configuration for the reverse proxy settings by copying the web server configuration into the load balancer.
 ![image](https://user-images.githubusercontent.com/58337007/155458745-055388f0-8af9-4ee0-a666-8f9e12e8a227.png)
 
 9. Removed the default site to allow the reverse proxy to redirect to the new configuration file using the cmdlet below:
 `sudo rm -f /etc/nginx/sites-enabled/default`
 
 10. Checked if nginx is successfully configured using the following cmdlet:
  `sudo nginx -t`
 ![image](https://user-images.githubusercontent.com/58337007/155459296-e64298f3-904f-4922-bd72-52a699c90a18.png)
 
 11. Navigated to the nginx sites-enabled folder using the cmdlet below:
  `cd /etc/nginx/sites-enabled/`
  
 12. Linked the Load Balancer config file created in the site-available to the site enabled so that the nginx can access the configuration to it using the cmdlet below:
  `sudo ln -s ../sites-available/load_balancer.conf`
 ![image](https://user-images.githubusercontent.com/58337007/155459930-551abff2-a050-4ce9-9227-6a031d9bcdd5.png)
 
 13. Reloaded nginx using the following cmdlet.
  `sudo systemctl reload nginx`


 
 
