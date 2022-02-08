# PROJECT6: WEB SOLUTION WITH WORDPRESS
PHP is currently the dominant web programming language used by more websites than any other programming language. In this project, I will prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress, a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).
Thre are two parts to this project:
  1. Configuring storage subsystem for Web and Database servers based on Linux OS.
  2. Installing WordPress and connecting it to a remote MySQL database server.
 For this project, I will be using RedHat OS in the EC2 servers.
 
 ## STEP 1 Prepare a Web Server:
 
 1. Launched an EC2 instance that will server as "Web Server". Created 3 volumes in the same AZ as my Web Server EC2, each of 10 GiB.
 
 Output of the above task:
 
![image](https://user-images.githubusercontent.com/58337007/152720974-8789ba94-7796-4e08-a03e-4b78d4f75c8f.png)

2. Attached all three volumes one by one to my Webserver EC2 instance as follows:

![image](https://user-images.githubusercontent.com/58337007/152720539-331b0237-8c7f-43ac-a511-225751c2c096.png)

3. SSH-d into the Cent-OS terminal in AWS as follows:

![image](https://user-images.githubusercontent.com/58337007/152720280-0ee34075-f172-42b4-a524-de51d4352663.png)

Checked the status of the 3 volumes by running the following cmdlet:

`lsblk`

Output of the command above:

![image](https://user-images.githubusercontent.com/58337007/152721154-f82190ca-3f9a-4c70-9fe1-af8c92d532a3.png)

Used the cmdlet below to inspect all mounts and free space on  my server:


`df -h`

Output of the above command:

![image](https://user-images.githubusercontent.com/58337007/152721359-b6b6e342-2417-4e2c-96fc-64f4bebcebb5.png)

4. Configured the 3 voluems usnig gdisk as such:

`sudo gdisk /dev/xvdf`

![image](https://user-images.githubusercontent.com/58337007/152722005-542fbbc3-2c27-4be9-af4c-bb456d3ff7b5.png)

`sudo gdisk /dev/xvdg`

![image](https://user-images.githubusercontent.com/58337007/152722297-7a1a3c4a-e8ae-4476-82f2-134481e2f52c.png)

`sudo gdisk /dev/xvdh`

![image](https://user-images.githubusercontent.com/58337007/152722466-96f4ca21-b80e-450a-8f2e-e222693d146a.png)

5. Ran the followin gcmdlet to confirm the configuration above:

`lsblk`

Output of the above command:

![image](https://user-images.githubusercontent.com/58337007/152722618-22727c8e-1fd5-422d-9fdd-3fc62d8e6464.png)

6. Used the cmdlets below to install the lvm2 package and to check for available partitions respectively :

`sudo yum install lvm2 -y`

![image](https://user-images.githubusercontent.com/58337007/152722857-535d779c-563d-4f57-b1c2-166088f0a2f9.png)

`sudo lvmdiskscan`

![image](https://user-images.githubusercontent.com/58337007/152723000-6c0005cc-03eb-42b7-a1b4-4acb5a396be7.png)

7. Used the pvcreate cmdlet to mark each of the 3 disks as physical volumes (PVs) to be used by LVM

`udo pvcreate /dev/xvdf1`

`sudo pvcreate /dev/xvdg1`

`sudo pvcreate /dev/xvdh1`

Output of running the above commands at once:

![image](https://user-images.githubusercontent.com/58337007/152723568-fd7f236c-bbfa-4801-af29-968a33ad2bda.png)

8. Used the cmdlet below to verify that my Physical Volume has been created successfully:

`sudo pvs`

![image](https://user-images.githubusercontent.com/58337007/152723735-9f56bab7-73ad-4e53-8d5f-29b57bf4ef57.png)

9. Ran the cmdlet below to add all 3 PVs to a volume group (VG); named the VG 'webdata-vg'

`sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![image](https://user-images.githubusercontent.com/58337007/152724188-5db071a7-2e5a-4800-9178-b0977c79a350.png)

10. Verified that the VG has been created successfully by running the cmdlet below:

`sudo vgs`

![image](https://user-images.githubusercontent.com/58337007/152724359-8a598344-1f12-4e5e-8415-d71cbfd75fd9.png)

11. Ran the cmdlet below to create Logical Volume within the VG 'webdata-vg' as follows:

`sudo lvcreate -n apps-lv -L 14G webdata-vg`

![image](https://user-images.githubusercontent.com/58337007/152724802-08ef5b8f-e303-488a-b090-04cb44e26bc8.png)

`sudo lvcreate -n logs-lv -L 14G webdata-vg`

![image](https://user-images.githubusercontent.com/58337007/152725039-c3fb777c-76c4-4807-b737-4626436e37cb.png)

12. Verified the Logical Volume has been created successfully by running the folowing cmdlet:

`sudo lvs`

![image](https://user-images.githubusercontent.com/58337007/152725248-f89916ca-fd36-4843-a797-a0bcad67b12d.png)

13. Verified the entire setup using the cmdlet below:

`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

`sudo lsblk`

![image](https://user-images.githubusercontent.com/58337007/152725657-995cead9-79ba-41a4-b436-e1171335c3b0.png)

![image](https://user-images.githubusercontent.com/58337007/152725734-c861c6d1-c204-4f1b-8085-43f513b60fbc.png)

14. Ran the following 'mkfs.ext4' to format the logical volumes with ext4 filesystem

`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![image](https://user-images.githubusercontent.com/58337007/152726179-a869aa75-dfeb-43f6-a10c-ce66d76bd1cd.png)

15. Created **/var/www/html** directory to store website files using the cmdlet below:

`sudo mkdir -p /var/www/html`

16. Created **/home/recovery/logs** directory to store backup of log data using the cmdlet below:

`sudo mkdir -p /home/recovery/logs`

![image](https://user-images.githubusercontent.com/58337007/152726837-1882d03c-47b2-49e2-a1e2-8d837bb31169.png)

17. Mounted **/var/www/html** on **apps-lv** logical volume using the cmdlet below:

`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

![image](https://user-images.githubusercontent.com/58337007/152727270-c91a29ef-6b2f-4def-9463-881f5db186bb.png)

18. Ran the following 'rsync' utility to backup all the files in the log directory **/var/log** into **/home/recovery/logs** (required before mounting the file system) using the cmdlet below:

`sudo rsync -av /var/log/ . /home/recovery/logs/

![image](https://user-images.githubusercontent.com/58337007/152728293-aaea9863-8601-4ced-9525-b2edd5d1a6ee.png)

19. Mounted **/var/log** on **logs-lv** logical volume. (Note that all the existing data on /var/log will be deleted. Hence Step 15 above is very important); using the following cmdlet:

![image](https://user-images.githubusercontent.com/58337007/152728895-294f62af-f47f-424f-bea3-86a79eafd5ea.png)

20. Restored log files back into **/var/log** directory using the cmdlet below:

`sudo rsync -av /home/recovery/logs/ . /var/log`

![image](https://user-images.githubusercontent.com/58337007/152729426-3c56b420-1193-4235-ba6b-b25b01e06235.png)

Ran the cmdlet below to check that the log files have been restored into the **/var/log** directory.

`sudo ls -l /var/log`

![image](https://user-images.githubusercontent.com/58337007/152729844-42721c54-022d-4d5d-9390-5a75893cc6a0.png)

21. Updated the /etc/fstab file so that the mount configuration will persist after restart of the server using the folowing cmdlet to obtain the UUID of the device:

`sudo blkid`

![image](https://user-images.githubusercontent.com/58337007/152731812-866245f2-d19c-4c14-a873-02b6feda0996.png)

`sudo vi /etc/fstab`

![image](https://user-images.githubusercontent.com/58337007/152731635-8e86af42-d268-47af-ab44-d651f25746ba.png)

22. Tested the configration and reloaded daemon using the cmdlets below:

`sudo mount -a`

`sudo systemctl daemon-reload`

![image](https://user-images.githubusercontent.com/58337007/152732175-708dde36-ad81-4a6c-87c6-a85a75df87bf.png)

23. Verified the setup by running the following cmdlet:

`df -h`

Output looked like this:

![image](https://user-images.githubusercontent.com/58337007/152732398-a8a4b272-c781-4004-ba05-4f2de0869593.png)


## STEP 2 Prepared a Database Server:

 1. Launched a second RedHat EC2 instance that will have the role - 'DB Server'.
 Repeated the same steps as for the Web Server, but instead of apps-lv created db-lv and mounted it to /db directory instead of /var/www/html/.
 
 2. Attached all three volumes one by one to my Webserver EC2 instance.

3. SSH-d into the 'DB Server' and checked the status of the three volumes attached to it using the cmdlet below:

`lsblk`

4. Configured the three volumes using 'gdisk' as such:

`sudo gdisk /dev/xvdf`

![image](https://user-images.githubusercontent.com/58337007/152889285-76790631-5299-4070-b332-b6282e267c08.png)

`sudo gdisk /dev/xvdg`

![image](https://user-images.githubusercontent.com/58337007/152890043-d58371df-5874-4b97-a451-008137bea04f.png)

`sudo gdisk /dev/xvdh`

![image](https://user-images.githubusercontent.com/58337007/152890247-dbc69a3b-da1e-4fa9-a7cc-6abd72bfb8d1.png)

5. Ran the followin gcmdlet to confirm the configuration above:

`lsblk`

Output of the above command:

![image](https://user-images.githubusercontent.com/58337007/152890446-e533f857-6fde-42e3-ba7e-29bba39d4497.png)

6. Used the cmdlets below to install the lvm2 package and to check for available partitions respectively :

`sudo yum install lvm2 -y`

![image](https://user-images.githubusercontent.com/58337007/152890601-4e9279f2-2ea4-4d6e-b64d-8677a44bef52.png)

`sudo lvmdiskscan`

![image](https://user-images.githubusercontent.com/58337007/152723000-6c0005cc-03eb-42b7-a1b4-4acb5a396be7.png)

7. Used the pvcreate cmdlet to mark each of the 3 disks as physical volumes (PVs) to be used by LVM

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

Output of the above cmdlet:

![image](https://user-images.githubusercontent.com/58337007/152890991-f50f18e2-9958-4bd0-82ae-0627c1a466a9.png)

8. Ran the cmdlet below to add all 3 PVs to a volume group (VG); named the VG 'database-vg'

`sudo vgcreate database-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![image](https://user-images.githubusercontent.com/58337007/152891477-82fdc656-ad2f-407a-8947-73d21a5fe547.png)

9. Ran the following cmdlet to create logical volume within the Volume Group (VG):

`sudo lvcreate -n db-lv -L 20G database-vg`

![image](https://user-images.githubusercontent.com/58337007/152892029-ff3f45e3-2e38-4139-b20a-84abbd09f151.png)

10. Used the cmdlet below to create a mount directory:

'sudo mkdir /db`

11. Ran the following 'mkfs.ext4' to format the logical volumes with ext4 filesystem

`sudo mkfs -t ext4 /dev/database-vg/db-lv`

![image](https://user-images.githubusercontent.com/58337007/152892677-3796273d-fdc1-450d-bcb9-89dedd3dc8b1.png)

12. Mounted the logical volume to the db directory as follows:

`sudo mount /dev/database-vg/db-lv /db`

![image](https://user-images.githubusercontent.com/58337007/152893044-129aa5e3-436b-4efe-9fc9-4e0c77c8c23e.png)

13. Updated the /etc/fstab file so that the mount configuration will persist after restart of the server using the folowing cmdlet to obtain the UUID of the device:

`sudo blkid`

`sudo vi /etc/fstab`

![image](https://user-images.githubusercontent.com/58337007/152893651-5186764b-d286-4327-b5f9-1a567506373c.png)

![image](https://user-images.githubusercontent.com/58337007/152894197-606fe81d-f75d-4824-9202-398fe28da35a.png)

14. Ran the following cmdlet to reload the daemon: 

`sudo systemctl daemon-reload`

![image](https://user-images.githubusercontent.com/58337007/152894442-5b1df768-dcf7-44e1-bd71-31367bd478e9.png)


 
 ## STEP 3 Installed WordPress on the Web Server EC2:
 
 1. Ran the following cmdlet to update the repository on both servers:

`sudo yum update -y`

![image](https://user-images.githubusercontent.com/58337007/152895538-dfc165da-b6f6-4131-b45e-c9a8f9a03cee.png)

![image](https://user-images.githubusercontent.com/58337007/152907237-84a55602-d7e8-4295-97b5-bb122b1a9a33.png)

2. Installed wget, PHP and its dependencies using the cmdlet below:

`sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y`

![image](https://user-images.githubusercontent.com/58337007/152908807-16876256-86d3-45ca-a2ac-e287018ee060.png)

`sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y`

![image](https://user-images.githubusercontent.com/58337007/152909289-dc6190a1-9ced-4d2a-b5ea-d131e31503fa.png)

Searched for **PHP** modules available for download by running the following cmdlet:

`sudo dnf module list php`

![image](https://user-images.githubusercontent.com/58337007/152909653-4d5b29ef-65e6-492b-8da8-481e73827368.png)

The current installed version of PHP is PHP 7.2; to install the newer release, PHP 7.4, I reset the PHP modules using the cmdlet below:

`sudo dnf module reset php`

![image](https://user-images.githubusercontent.com/58337007/152910128-52b49c5c-ab02-4737-aba5-05e3ba11b8c4.png)

Enabled PHP 7.4 byrunning the cmdlet below:

`sudo dnf module enable php:remi-7.4`

![image](https://user-images.githubusercontent.com/58337007/152910561-2f12f03c-382d-4c04-836e-d726649f1107.png)

Ran the cmdlet below to install **PHP, PHP-FPM** (FastCGI Process Manager) and associated PHP modules:

`sudo dnf install php php-opcache php-gd php-curl php-mysqlnd`

![image](https://user-images.githubusercontent.com/58337007/152910807-8f3eb308-8a43-4bcf-b280-3a39b7999714.png)

Checked **PHP** version using the cmdlet below:

`php -v`

![image](https://user-images.githubusercontent.com/58337007/152911278-56038f37-7dac-4a40-b01a-2de4b0d847a8.png)

Started and enabled PHP as follows:

`sudo systemctl start php-fpm`

`sudo systemctl enable php-fpm`

![image](https://user-images.githubusercontent.com/58337007/152911651-73bc026c-47d4-4d17-a9dc-867439ece2bc.png)

Checked PHP status by running the following command:

`sudo systemctl status php-fpm`

![image](https://user-images.githubusercontent.com/58337007/152911833-197f4277-34db-4bca-8b8e-6e297295e6be.png)

Ran the cmdlet below to instruct **SELinux** to allow **Apache** to execute the **PHP** code via **PHP-FPM** run.

`setsebool -P httpd_execmem 1`

![image](https://user-images.githubusercontent.com/58337007/152912543-b4650e68-027c-4a50-8ee0-4c167b194c4c.png)

Ran the cmdlet below to start httpd and check status:

`sudo systemctl start httpd`

`sudo systemctl status httpd`

![image](https://user-images.githubusercontent.com/58337007/152912826-3b9b119c-585b-4aad-a018-4becade072d1.png)

Created a 'wordpress' directory using the cmdlet below and cd into it:

`mkdir wordpress`

Downloaded wordpress into the **wordpress** directory using the cmdlet below:

`sudo wget http://wordpress.org/latest.tar.gz`

![image](https://user-images.githubusercontent.com/58337007/152913769-8cad2796-b216-48b2-a047-113c18c8136f.png)

**All wordpress configurations will be done in this folder before being copied over to /var/www/html folder**

Extracted wordpress using the cmdlt below:

`sudo tar xzvf latest.tar.gz`

![image](https://user-images.githubusercontent.com/58337007/152914130-3b10e69e-c160-484e-9bd9-e6a1ad239e46.png)

cd into the wordpress folder created from the step above using the cmdlet below:

`cd wordpress/`

![image](https://user-images.githubusercontent.com/58337007/152914494-b79c83d2-9196-4d73-a7a1-fc47e01f2501.png)

Copied over the contents of wp-config-sample.php to wp-config.php using the cmdlet beow:

`sudo cp -R wp-config-sample.php wp-config.php`

![image](https://user-images.githubusercontent.com/58337007/152915427-b0780dab-94ef-474b-a699-a01bcbe4e811.png)

cd into wordpress folder in root as follows:

`cd ..`

Copied over the 'wordpress' folder content to **/var/www/html/** using the cmdlet below:

`sudo cp -R wordpress/. /var/www/html/`

cd into the /var/www/html/ and checked the content using cmdlet below:

`ls -l /var/www/html/`

![image](https://user-images.githubusercontent.com/58337007/152916601-3fefdc3f-a212-4cc8-b683-024a1bced35d.png)

Installed mysql-server in the web server using the cmdlet below:

`sudo yum install mysql-server -y`

![image](https://user-images.githubusercontent.com/58337007/152917000-d83fe1fb-e47d-48be-bb0d-7d8e3fda8153.png)

Started, enabled and checked status of the mySQL server in the web server usng the cmdlet below:

`sudo systemctl start mysqld`

`sudo systemctl enable mysqld`

`sudo systemctl status mysqld`

![image](https://user-images.githubusercontent.com/58337007/152917989-8c5a380a-f868-46ad-80f1-a5de5fa8ed7c.png)

Updated the Web Server wp-config.php file as follows:

`sudo vi wp-config.php`

![image](https://user-images.githubusercontent.com/58337007/152922332-f903eb2a-a242-485e-b345-5131b56c61b5.png)

Disabled the Default Apache welcome page by re-naming it using the cmdlet below:

`sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup`

![image](https://user-images.githubusercontent.com/58337007/152923141-00974c2d-8cb8-48af-8a15-429befbc1ae8.png)

Tested that the web server can communicate with the database server using the following cmdlet via port 3306:

`sudo mysql -h 172.31.29.161 -u wordpress -p`

![image](https://user-images.githubusercontent.com/58337007/152923864-51589f3c-41f5-4f14-95aa-fa5570a0b30d.png)

Changed permissions and configurations so Apache can use Wordpress as follows:

`sudo chown -R apache:apache /var/www/html/`

![image](https://user-images.githubusercontent.com/58337007/152924598-fbbf33ea-352e-4181-8558-e222aeaa1db5.png)

Applied the following security policies on the webserver:

`sudo chown -R apache:apache /var/www/html/`

`sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R`

`sudo setsebool -P httpd_can_network_connect=1`

`sudo setsebool -P httpd_can_network_connect_db_1`








## STEP 4 Installed MySQL on the Database Server EC2:

1. Ran the following cmdlet to install MySQL server in the database server:

`sudo yum install mysql-server -y`

![image](https://user-images.githubusercontent.com/58337007/152917555-7056c56c-4727-4362-946a-d211f00f847e.png)

2. Started, enabled and checked status of the mySQL server in the database server usng the cmdlet below:

`sudo systemctl start mysqld`

`sudo systemctl enable mysqld`

`sudo systemctl status mysqld`

![image](https://user-images.githubusercontent.com/58337007/152918408-2549d44f-e253-4ed3-91e4-ac1871d46070.png)

3. Securely deployed mySQL server in the database server using the cmdlet below:

`sudo mysql_secure_installation`

![image](https://user-images.githubusercontent.com/58337007/152918706-16ef7c45-09b0-4306-a428-817aae2c8c95.png)

4. Logged into the mysql server in the database server as root user and supplied password:

`sudo mysql -u root -p`

![image](https://user-images.githubusercontent.com/58337007/152919192-ad3b3399-d616-4cde-904f-fe19a784f89f.png)

5. Created database in MySQL server using the sql script below:

`Create database wordpress;`

![image](https://user-images.githubusercontent.com/58337007/152919544-bfb53840-1c91-4f4d-be4f-b7b59fc1d061.png)

6. Created database user as follows:

`CREATE USER 'wordpress'@'%' IDENTIFIED WITH mysql_native_password BY 'wordpress';`

![image](https://user-images.githubusercontent.com/58337007/152920758-fd2ef622-8128-4bc5-be11-450b85baf8da.png)

7. Updated the mysql server application file to allow connection from any server as follows:

`sudo vi /etc/my.cnf`

![image](https://user-images.githubusercontent.com/58337007/152921388-71403e1a-8fec-494f-877e-53243e798255.png)

Restarted mysqld using the following cmdlet:

`sudo systemctl restart mysqld`



Finally refreshed the WordPress (formerly Apache default page) page and set it up:

![image](https://user-images.githubusercontent.com/58337007/152926540-c1129cd5-7cef-4868-bccd-52461135aea1.png)

![image](https://user-images.githubusercontent.com/58337007/152926568-718b40fe-be7d-4324-9bed-fe20d9f4aa45.png)

![image](https://user-images.githubusercontent.com/58337007/152926584-90c793ca-70ab-4c7a-9255-c59eb325865e.png)

![image](https://user-images.githubusercontent.com/58337007/152926612-16c3a530-7672-4f82-811b-9c5a97e6b770.png)




 
