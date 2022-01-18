# Project 2 WEB STACK IMPLEMENTATION (LEMP STACK)

**Step 0** - Launched Git Bash and ran the following command:

'ssh -i <my-private-key.pem> ubuntu@54.159.221.53

![screenshot](https://user-images.githubusercontent.com/58337007/149377464-999f138a-92aa-4d66-b1d3-90b6ce478a8b.png)


**Step 1** - Installing the NGINX Web Server using the 'apt' package manger':
---

The below cmdlet updates a list of packages in package manager

`$ sudo apt update`

![screenshot](https://user-images.githubusercontent.com/58337007/149385628-23c9c452-1941-4d11-bf47-6532ccb3223c.png)

The below cmdlet runs ngnix package installation

`$ sudo apt install nginx`

![screenshot](https://user-images.githubusercontent.com/58337007/149386103-62f6cdec-1de1-4551-b6b4-b2c2d681163d.png)

This code verifies that nginx is running as a Service in my Ubuntu Server

`$ sudo systemctl status nginx`

![screenshot](https://user-images.githubusercontent.com/58337007/149386675-cd4df6fb-5e36-4b0e-b944-d616b0666c53.png)

Opened TCP port 80 in the EC2 Instance which is the default port that web browsers use to access web pages on the Internet

![screnshot](https://user-images.githubusercontent.com/58337007/149387581-92fac8c6-9537-4d17-b05f-5b20bd0c1b4c.png)

Checked if I can access nginx locally in my Ubuntu shell by running the following cmdlet

`curl http://localhost:80`
or
'curl http://127.0.0.1:80`


Output to check if we can access the nginx locally

![screenshot](https://user-images.githubusercontent.com/58337007/149394327-5ad8a390-da49-4afe-bba8-4a842a534c10.png)

![screenshot](https://user-images.githubusercontent.com/58337007/149394585-ceef2aed-214b-4a9e-8cca-2ad6f0e74cd1.png)

Tested that Nginx server can respond to requests from the Internet by accessing the url

`http://54.159.221.53:80`

Output to check if we can access the nginx over the internet from any IP address

![screenshot](https://user-images.githubusercontent.com/58337007/149395924-163cf7ae-2dc1-40e9-bcdb-cf295195091a.png)

Checked that I can retrieve the Public IP address via command line using the following cmdlet

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

![screenshot](https://user-images.githubusercontent.com/58337007/149396566-4f096439-529d-4846-9828-0cdf06c89ad0.png)

Test that nginx server is running over the browser

![screenshot](https://user-images.githubusercontent.com/58337007/149395924-163cf7ae-2dc1-40e9-bcdb-cf295195091a.png)



**Step 2** - Installing MySQL

The below cmdlet installs the mysql in the server

`$ sudo apt install mysql-server`

![screenshot](https://user-images.githubusercontent.com/58337007/149397302-f093c7f6-4135-4d0a-86b5-f8e44d1832ad.png)

![screenshot](https://user-images.githubusercontent.com/58337007/149397416-e5844afa-4cab-4ffa-88b6-2d64d8d3701c.png)

The below cmdlet runs a security script that removes sime insecure default settings and lock down access to the database system

`$ sudo mysql_secure_installation`

![screenshot](https://user-images.githubusercontent.com/58337007/149398008-0934a8cf-3db2-43f0-9d4b-d29d9969548d.png)

![screenshot](https://user-images.githubusercontent.com/58337007/149398182-41fe6304-582d-4055-b0b1-1a4f698fe945.png)


This code verifies that we can log in to the MySQL server

`$ sudo mysql`

![screenshot](https://user-images.githubusercontent.com/58337007/149398413-24072e11-6bcd-4ff4-88bd-327f19634d19.png)



**Step 3** - Installing PHP

The below cmdlet installs *php* ,  and *php-mysql*

`$ sudo apt install php-fpm php-mysql`

![screenshot](https://user-images.githubusercontent.com/58337007/149399766-ee3ae91e-dd7a-41ae-b588-5022db869baa.png)


Use the below to confirm your php version 

`$ php -v`

![screenshot](https://user-images.githubusercontent.com/58337007/149400129-5af78a71-4058-4b69-ad70-8d876226d0b1.png)




**Step 4** - Configuring Nginx to use PHP Processor


Created the root web directory for your_domain using ‘mkdir’ command

`$ sudo mkdir /var/www/projectLEMP`

Assigned ownership to the directory with this variable $USER which still referenced the system user

`$ sudo chown -R $USER:$USER /var/www/projectLEMP`

Created and opened a new configuration file in Nginx's sites-available directory using "nano" command-line editor

`$ sudo nano /etc/nginx/sites-available/projectLEMP`

Pasted the below bare-bones configuration text:

```#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

```

### To save and close the file, simply follow the steps below:

- Type CTRL+X
- Type y
- Hit ENTER to confirm

Used the below cmdlet to activate my configuration by linking to the config file form Nginx's sitest-enabled directory. This tells Nginx to use the configuration next time it is reloaded

`$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

![screenshot](https://user-images.githubusercontent.com/58337007/149402886-7a478b1f-4293-48e5-a86b-96d4ba5aa4c3.png)

Ran the following cmdlet to test my configuration for syntax errors

`sudo nginx -t`

Output message from running the above command

![screenshot](https://user-images.githubusercontent.com/58337007/149403397-ef2752ca-fc63-4d47-9f16-e50033ad35a1.png)

Disabled the default Nginx host that is currently configured to listen on port 80 by running the following cmdlet

`sudo unlink /etc/nginx/sites-enabled/default`

Reloaded Nginx to apply the changes by running the following cmdlet

`sudo systemctl reload nginx`

Outputs of the above commads:

![screenshot](https://user-images.githubusercontent.com/58337007/149405656-c0b809cf-5ec1-4cb3-8c81-c421e3fdc820.png)

Created an index.html file in the web root /var/www/projectLEMP directory to test that our new server block works as expected by running the following cmdlet.

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

Checked browser to open the website URL using ip address:

`http://54.159.221.53:80`

Output of website URL IP address:

![screenshot](https://user-images.githubusercontent.com/58337007/149406385-37742130-ee73-43fa-ad53-476f3c0d6b6d.png)

///**LEMP stack has been installed completely and it is operational**////


**Step 5** - Testing PHP with Nginx

Created a test PHP file in our document root and called it info.php within the document root in our text editor using nano by running the following cmdlet

`sudo nano /var/www/projectLEMP/info.php`

Typed the following lines in the new file to return information about my server.

```
<?php
phpinfo();

```
![screenshot](https://user-images.githubusercontent.com/58337007/149408065-54747b21-6654-44f5-83a0-89f8a897f6d0.png)

Accessed the php info page via the browser by vising the domain name or public IP address that I set up in the Nginx configuration file, followed by /info.php as follows:

`http://ec2-54-159-221-53.compute-1.amazonaws.com/info.php

Output of the web browser

![screenshot](https://user-images.githubusercontent.com/58337007/149408829-3e018239-cb1c-4eeb-94c2-61cb32b73e78.png)


Ran the following cmdlet to remove the file info.php file as it contains sensitive information about my PHP environment and Ubuntu server.

`sudo rm /var/www/ec2-54-159-221-53.compute-1.amazonaws.com/info.php`


**Step 6** - Retrieving data from MySQL database with PHP

Connected to the MySQL console using the root acount by running the cmdlet

`sudo mysql`

![screenshot](https://user-images.githubusercontent.com/58337007/149410712-56ceaca0-3a0b-4fbc-8e5d-4705d174b61b.png)

Created a new database by running the following cmdlet from the MySQL console:

mysql> `CREATE DATABASE `lemp_database`; 

Created a new user named lemp_user using mysql_native_password as default authentication method by running the following cmdlet:

`mysql>  CREATE USER 'lemp_user'@'%' IDENTIFIED WITH mysql_native_password BY 'DevOps2022';

Granted the newly created user permission over the lemp_database using the cmdlet:

mysql> GRANT ALL ON lemp_database.* TO 'lemp_user'@'%';

Outputs of running the commands above in mysql console:

![screenshot](https://user-images.githubusercontent.com/58337007/149441330-e19587bb-57d9-4fb5-a9f8-dd8f3c730e77.png)

Tested that the new user has the proper permissions by logging in to the MySQL console again using the custom user credentials:

`mysql -u lemp_user -p`

Output of user access to the lemp_database

![screenshot](https://user-images.githubusercontent.com/58337007/149442091-2ca8019f-36cc-4404-89c9-758f232fcac6.png)

Created a test table named todo_list running the following statement in the MySQL console:

```
`CREATE TABLE lemp_database.todo_list (
  item_id INT AUTO_INCREMENT,
  content VARCHAR(255),
  PRIMARY KEY(item_id)
);`
```
Output of running script above:

![screenshot](https://user-images.githubusercontent.com/58337007/149444677-7f8c82ae-7bd7-4a74-9444-df62211c2d6b.png)

Inserted a few rows of content in the table and confirmed that the data was successfully saved to the table by running:

mysql> SELECT * FROM lemp_database.todo_list;

Output of the above command:

![screenshot](https://user-images.githubusercontent.com/58337007/149446710-64313be0-278c-44c8-9e09-f4061689f452.png)

Created a PHP script that will connect to MySQL and query for your content and created a new PHP file in the custom web root directory using nano:

```
<?php
$user = "lemp_user";
$password = "DevOps2022";
$database = "lemp_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}

```

Accessed the php page in web browser by visiting the domain name or public IP address configured for our website, followed by /todo_list.php:

![screenshot](https://user-images.githubusercontent.com/58337007/149449755-a0576359-af8f-4b3c-9d0b-bf5a1055789b.png)

The PHP environment is ready to connect and interact with the MySQL server.



