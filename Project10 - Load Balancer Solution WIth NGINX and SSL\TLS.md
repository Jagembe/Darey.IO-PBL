# CONFIGURED NGINX AS A LOAD BALANCER
1. Created a fresh installation of Linux for NGINX (EC2 VM based on Ubuntu Server 20.04 LTS and named it Nginx LB, opened TCP port 80 for HTTP connections and TCP pport 443 ofr secured HTTPS conections.
![image](https://user-images.githubusercontent.com/58337007/155448141-9d4e2558-6872-419d-a78e-bdbe132bf2ef.png)
 
2. Registered domain name 'toolingeon.ml' and created a Route 53 hosted zone.
![image](https://user-images.githubusercontent.com/58337007/158031717-4849c211-00ca-4ccb-a7c3-7fa1f5facedf.png)
 
3. Updated the DNS Server names in the Domain record name with the values from the AWS Route 53.
![image](https://user-images.githubusercontent.com/58337007/158031758-37a92f7b-ad9b-406c-abef-bc48c57f846e.png)

4. Created a record in AWS Route 53 to point to the Load Balancer public IP. 
![image](https://user-images.githubusercontent.com/58337007/158031824-af430d4e-c1cb-4adb-b147-7cbf6f46e1b3.png)

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
 ![image](https://user-images.githubusercontent.com/58337007/158032316-5d7026b3-caa2-4f65-9da1-fbf1f9f64eab.png)
 
 8. Created a configuration for the reverse proxy settings by copying the web server configuration into the load balancer using the followng cmdlet:
 `sudo nano /etc/ngingx/sites-available/load_balancer.conf`
 Created a config file in the NGINX sites-available folder as follows:
 ![image](https://user-images.githubusercontent.com/58337007/158222857-085a14a2-85f7-4000-a0ac-22c511a55975.png)
 
 9. Removed the default site to allow the reverse proxy to redirect to the new configuration file using the cmdlet below:
 `sudo rm -f /etc/nginx/sites-enabled/default`
 ![image](https://user-images.githubusercontent.com/58337007/158223432-122c89e0-2309-4899-afae-3d4391776637.png)

 10. Checked if nginx is successfully configured using the following cmdlet:
  `sudo nginx -t`
 ![image](https://user-images.githubusercontent.com/58337007/158223620-c22f25e2-3293-43c2-aef3-39ce2263fd80.png)
 
 11. Navigated to the nginx sites-enabled folder using the cmdlet below to link the config file created in the sites-available with the sites-enabled:
  `cd /etc/nginx/sites-enabled/`
  
 12. Linked the Load Balancer config file created in the site-available to the site enabled so that the nginx can access the configuration to it using the cmdlet below:
  `sudo ln -s ../sites-available/load_balancer.conf`
 ![image](https://user-images.githubusercontent.com/58337007/158247777-739240d2-8552-4af3-a559-e01389563b74.png)
 ![image](https://user-images.githubusercontent.com/58337007/158248262-475e47bf-5a83-4b04-a37f-a68b4b249c48.png)

 13. Reloaded nginx using the following cmdlet.
  `sudo systemctl reload nginx`
 ![image](https://user-images.githubusercontent.com/58337007/158248514-02b7c88a-30eb-48ca-bb7b-79eadc5c5810.png)
 
 14. Checked the 'http://toolingeon.ml' domain/site and saw that it redirects to the tooling page.
 ![image](https://user-images.githubusercontent.com/58337007/158248702-df68b3e3-4e53-4e98-b0bf-e418e346131d.png)
 
 ## CONFIGURED SECURED CONNECTION USING SSL/TLS CERTIFICATES AS FOLLOWS:
 
 1. Ran the following cmdlet below to install the certbot software.
 `sudo apt install certbot -y`
 ![image](https://user-images.githubusercontent.com/58337007/158033296-801c8541-d763-468e-80bf-be413f0b0c71.png)
 
 2. Installed a python dependency module to be used by the certbot using the following cmdlet:
 `sudo apt install python3-certbot-nginx -y`
 ![image](https://user-images.githubusercontent.com/58337007/158250026-312689d0-c3ea-44ac-a9e4-1cd7c581fc6c.png)
 
 3. Checked nginx syntax again and reloaded after the installation of certbot and python3 dependency.
 `sudo nginx -t && sudo nginx -s reload`
 ![image](https://user-images.githubusercontent.com/58337007/158250284-2239a42e-49a1-4053-ba00-e5afa320d89b.png)

 4. Created a certificate for our domain to secure it by running the following cmdlet:
 `sudo certbot --nginx -d toolingeon.ml -d www.toolingeon.ml`
 ![image](https://user-images.githubusercontent.com/58337007/158251471-ef5e94a2-03fa-4948-9562-1ac73634510b.png)
 
 5. Refreshed the 'toolingeon.ml' web page to observe the applied security settings after creating certificate
 ![image](https://user-images.githubusercontent.com/58337007/158252121-a2c87a10-5168-432d-bd5e-9433b1b7db72.png)
 ![image](https://user-images.githubusercontent.com/58337007/158252551-430b4d01-798a-4461-9e21-2960691eed36.png)
 ![image](https://user-images.githubusercontent.com/58337007/158252832-1a5f9f86-00fd-4200-8fee-d5ae95b0035b.png)
 
 6. As best practice, created a cronjob that will run a renew command periodically to automatically renew our certificate by editing the crontab file as follows:
 `crontab -e`
 ![image](https://user-images.githubusercontent.com/58337007/158254041-720f7262-39c5-4a45-abb5-219a2ed98bd7.png)
 Added the following script to the file:
 `* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`
 ![image](https://user-images.githubusercontent.com/58337007/158254211-49870b32-1f17-4990-81c7-693f6fa325e9.png)
 
 We have created an NGINX load balancer and can route traffic to it via reverse proxy and also secured it.
 



 
 
