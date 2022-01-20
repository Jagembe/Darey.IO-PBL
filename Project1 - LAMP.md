# Project 1: WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

**Step 1** - Installing Apache using Ubuntu's package manager 'apt':
---

The below cmdlet updates a list of packages in package manager

`$ sudo apt update`

![screenshot](https://user-images.githubusercontent.com/58337007/149040066-f38cf819-9b45-45d3-a733-b3354ebbbf84.png)

The below cmdlet runs apache2 package installation

`$ sudo apt install apache2`

![screenshot](https://user-images.githubusercontent.com/58337007/149041146-3158a11b-1335-42ca-b654-80fa1108ddca.png)
![screenshot](https://user-images.githubusercontent.com/58337007/149040886-0d146712-8961-4ac4-868b-08520e94c0d0.png)

This code verifies that apache2 is running as a Service in my Server

`$ sudo systemctl status apache2`

![Output of the apache2 installation](https://user-images.githubusercontent.com/58337007/149041467-a28e00bd-2fe4-4af0-aa40-3eba89d27702.png)

Opened TCP port 80 in the EC2 Instance which is the default port that web browsers use to access web pages on the Internet

![screenshot](https://user-images.githubusercontent.com/58337007/149042141-e17d6d3f-703f-4a48-9e2c-8a9236dc1479.png)


Output to check if we can access the apache2 locally

![screenshot](https://user-images.githubusercontent.com/58337007/149042345-aaf69c37-1538-407a-9081-53a3441ac78c.png)

Output to check if we can access the apache2 over the internet from any IP address

![screenshot](https://user-images.githubusercontent.com/58337007/149042520-81355702-c22e-4e9e-8198-3302415a63fb.png)


Test that apache server is running over the browser

![screenshot](https://user-images.githubusercontent.com/58337007/149043462-008f558a-404e-4a04-9484-16bf7792ac8c.png)



**Step 2** - Installing MySQL

The below cmdlet installs the mysql in the server

`$ sudo apt install mysql-server`

![screenshot](https://user-images.githubusercontent.com/58337007/149043751-c0b393c5-fd8d-4aca-8846-cc50245ecc18.png)

The below cmdlet runs a security script that removes sime insecure default settings and lock down access to the database system

`$ sudo mysql_secure_installation`

![screenshot](https://user-images.githubusercontent.com/58337007/149063831-21500429-e1b8-45a9-bb3a-c3c0adc04646.png)


This code verifies that we can log in to the MySQL server

`$ sudo mysql`

![screenshot](https://user-images.githubusercontent.com/58337007/149064135-ca88e451-416f-4789-a571-1ceca65d7d17.png)




**Step 3** - Installing PHP

The below cmdlet installs *php* , *libapache2-mod-php* and *php-mysql*

`$ sudo apt install php libapache2-mod-php php-mysql`

![screenshot](https://user-images.githubusercontent.com/58337007/149064681-2c92ef92-945b-47e9-be25-b8e3f624488b.png)


Use the below to confirm your php version 

`$ php -v`

![screenshot](https://user-images.githubusercontent.com/58337007/149064868-50eed504-d327-48f2-a081-4e6ddee6dbc9.png)


**LAMP stack has been installed completely and it is operational**



**Step 4** - Creating a Virtual Host for your Website using Apache 2


Created a directory for projectlamp using ‘mkdir’ command

`$ sudo mkdir /var/www/projectlamp`

Assigned ownership to the directory with this variable $USER which still referenced the system user

`$ sudo chown -R $USER:$USER /var/www/projectlamp`

Created and opened a new configuration file in Apache’s sites-available directory using "vi" command-line editor

`$ sudo vi /etc/apache2/sites-available/projectlamp.conf`

Pasted the below bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and pasted the text:

```xml

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

### To save and close the file, simply follow the steps below:

- Hit the esc button on the keyboard
- Type :
- Type wq. w for write and q for quit
- Hit ENTER to save the file

Used the below cmdlet to show the new file we created in the sites-available directory

`$ sudo ls /etc/apache2/sites-available`

See screenshot of the above cmds ran

![screenshot](https://user-images.githubusercontent.com/58337007/149066213-0ffc08ed-cfe1-4bce-bafb-2e017f8a8cc5.png)



Enabled the new virtualhost created with the below

`$ sudo a2ensite projectlamp`

Disabled the default website that comes installed with Apache with the below

`$ sudo dissite 000-default`

![screenshot](https://user-images.githubusercontent.com/58337007/149066858-a484288d-e782-4eec-ad33-77b7aa04b2fa.png)


Ran the below cmd to make sure the config file does not contain any syntax error

`$ sudo apache2ctl configtest`

![screenshot](https://user-images.githubusercontent.com/58337007/149067167-072cfaba-cadc-4954-a4d2-baa7930413c6.png)

Used the below to reload Apache so the changes can take effect

`$ sudo systemctl reload apache2`


![screenshot](https://user-images.githubusercontent.com/58337007/149067365-0a5642e6-b060-48fd-a453-ec80e10239e0.png)


Created an index.html file in the */var/www/projectlamp* location for testing if the virtual host works fine

`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`


The below screen shot is the output which shows Virtual host is working properly


![screenshot](https://user-images.githubusercontent.com/58337007/149067620-bdf71fdb-d15d-4a81-ba9d-9f1b87546884.png)




**Step 5** - Enable PHP on the website

We needed to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive so as to allow the php page be the landing page

`sudo vim /etc/apache2/mods-enabled/dir.conf`

```xml

<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

```

![screenshot](https://user-images.githubusercontent.com/58337007/149068937-14885820-7eac-4ced-a0f8-e7621295617a.png)


Created a new file named index.php inside your custom web root folder:

`$ vim /var/www/projectlamp/index.php`


```php

<?php
phpinfo();

```

Reloaded apache for changes to take place

`$ sudo systemctl reload apache2`

![screenshot](https://user-images.githubusercontent.com/58337007/149070554-e0108bbb-dff1-489b-9559-ba82fc468524.png)



The output shows that PHP installation is working as expected

![PHP landing](https://user-images.githubusercontent.com/58337007/149070281-03e963cd-eeb7-4c98-bfe6-6870fe85c2cd.png)

