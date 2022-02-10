# STEP 1 - PREPARED NFS SERVER:
1. Spun up 4 EC2 Servers and purposed as follows:
    - **Webservers**  x3 - Labeled WEBSERVER 1, WEBSERVER 2, WEBSERVER 3; all RedHat Enterprise Linux 8 OS
    - **NFS Server**  x1 - Labeled NFS - RedHat Enterprise Linux 8 OS
    - **DB**          x1 - Labeled DB - Ubuntu Linux (20.04 LTS)
 
 Output of the above step:
 
 ![image](https://user-images.githubusercontent.com/58337007/153329606-2eb6f2e2-34d9-4186-9f17-559fa0e815b3.png)
 
2. Using LVM experience from Project 6, configured LVM on the NFS Server as follows:

  - Created 3 Volumes in the same availability zone as the NFS Server
  - Attached all 3 Volumes to the NFS Server
  - SSH into the NFS Server
  - Lised all the block devices attached to the NFS server by running the following cmdlet:
  
    `lsblk`
  
   ![image](https://user-images.githubusercontent.com/58337007/153333715-8cfc01e6-bb4d-4e8d-b0ca-4cd1ee9455f3.png)
   
   - Used the gdisk utility to create a partiotion on each of the volumes (**/dev/xvdff, /dev/xvdg/, /dev/xvdh)**:
   
   ![image](https://user-images.githubusercontent.com/58337007/153334509-6683feed-fb86-4e1c-98d0-3cdb784c387f.png)
    
   ![image](https://user-images.githubusercontent.com/58337007/153334762-39ccda7f-76bc-4d1d-913c-81cbd8f92bdf.png)
   
   ![image](https://user-images.githubusercontent.com/58337007/153334985-f0539857-ce5f-4993-a264-e25c62d24b2b.png)
   
    - Checked to confirm that all partitions are present
    
   ![image](https://user-images.githubusercontent.com/58337007/153335216-0da54881-fea7-467a-8794-34fe964978fc.png)
   
3. Installed the LVM package by running the cmdlet below:

`sudo yum install lvm2 -y`

![image](https://user-images.githubusercontent.com/58337007/153335784-84bc14f7-c609-46a4-85c3-21f52d469eb1.png)

4. Ran the following command to check for available partitions:

`sudo lvmdiskscan`

![image](https://user-images.githubusercontent.com/58337007/153335905-6fbaa150-c351-4cd6-8a4c-bd4121834eaa.png)
  
5. Created Physical volumes to be used by LVM using the pvcreate utility as follows:

`sudo pvcreate /dev/xvdf1`

`sudo pvcreate /dev/xvdg1`

`sudo pvcreate /dev/xvdh1` 

![image](https://user-images.githubusercontent.com/58337007/153336610-b5a43a19-9b07-4393-ac68-ae16c61898bf.png)

Checked that the Physical volumes are created by running the cmdlet below:

`sudo pvs`

![image](https://user-images.githubusercontent.com/58337007/153336800-5e41130b-1250-4d9e-bfe0-6d310708af0e.png)

6. Created **Volume Group** using the **vg utility** by running the cmdlet below:

`sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![image](https://user-images.githubusercontent.com/58337007/153337276-63f99a00-da7d-4660-833a-6d950a780f43.png)

Checked that the volume group was created by running the cmdlet below:

`sudo vgs`

![image](https://user-images.githubusercontent.com/58337007/153337491-d0b4eb9c-def2-411e-9aeb-2676070dfca1.png)

7. Created 3 **Logical Volumes** by running the cmdlets below:

`sudo lvcreate -n lv-apps -L 9G webdata-vg`

`sudo lvcreate -n lv-logs -L 9G webdata-vg`

`sudo lvcreate -n lv-opt -L 9G webdata-vg`

![image](https://user-images.githubusercontent.com/58337007/153338100-abd42e63-ec20-4998-8821-2436b1cad78e.png)

Checked the Logical Volumes created by running the cmdlet below:

`sudo lvs`

`lsblk`

![image](https://user-images.githubusercontent.com/58337007/153338328-0c4c9504-f0c4-4a38-987a-d3747d6c1327.png)

`sudo vgdiplay -v`

![image](https://user-images.githubusercontent.com/58337007/153338739-c3cd899d-38ec-4708-bf88-326967aea351.png)

8. Formatted the disk as 'xfs' and not 'ext4' by running the cmdlet below:

`sudo mkfs -t xfs /dev/webdata-vg/lv-apps`

`sudo mkfs -t xfs /dev/webdata-vg/lv-logs`

`sudo mkfs -t xfs /dev/webdata-vg/lv-opt`

![image](https://user-images.githubusercontent.com/58337007/153339374-ec755e14-ea12-4d1c-85f2-2ea17c8e2f67.png)

9. Created the following mount points on /mnt directory for the logical volumes as follows:

`sudo mkdir /mnt/apps`

`sudo mkdir /mnt/logs`

`sudo mkdir /mnt/opt`

`sudo mount /dev/webdata-vg/lv-apps /mnt/apps`

`sudo mount /dev/webdata-vg/lv-logs /mnt/logs`

`sudo mount /dev/webdata-vg/lv-opt /mnt/opt`

![image](https://user-images.githubusercontent.com/58337007/153340487-84b4f14c-600a-470d-8b7c-aa5b89eba68f.png)

10. Installed NFS Server, configured it to start on reboot and make sure it is up and running as using the cmdlets below:

`sudo yum -y update`

![image](https://user-images.githubusercontent.com/58337007/153342524-3bc7c012-b144-488a-97ed-2c614d671e78.png)

`sudo yum install nfs-utils -y`

![image](https://user-images.githubusercontent.com/58337007/153347445-5d74c23e-80ce-4d6e-9a77-90d41bc7712a.png)

`sudo systemctl start nfs-server.service`

`sudo systemctl enable nfs-server.service`

`sudo systemctl status nfs-server.service`

![image](https://user-images.githubusercontent.com/58337007/153347749-924761dd-389b-4c60-9d0b-9a3d0d985daa.png)

11. Exported the mounts for webservers' subnet cidr to connect as clients. For simplicity, I will install all three Web Servers inside the same subnet, 
but in production set up I would probably want to separate each tier inside its own subnet for higher level of security.

Set up permission that will allow the Web servers to read, write and execute files on NFS by running the cmdlets below:

`sudo chown -R nobody: /mnt/apps`
`sudo chown -R nobody: /mnt/logs`
`sudo chown -R nobody: /mnt/opt`

`sudo chmod -R 777 /mnt/apps`
`sudo chmod -R 777 /mnt/logs`
`sudo chmod -R 777 /mnt/opt`

`sudo systemctl restart nfs-server.service`

![image](https://user-images.githubusercontent.com/58337007/153349155-ec3f5787-569a-409a-9f76-9d52d2d4346a.png)

12. Configured access to NFS for clients within the same subnet (Subnet CIDR – 172.31.80.0/20 ) by running the following cmdlets:

`sudo vi /etc/exports`

updated with the following code:

```
/mnt/apps 172.31.80.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

```

`Esc + :wq!`

![image](https://user-images.githubusercontent.com/58337007/153350017-5c94ff36-33da-448f-9560-752d62ec681a.png)

`sudo exportfs -arv`

![image](https://user-images.githubusercontent.com/58337007/153350308-047866d1-3a13-47d8-8ba8-c2ccd3e43770.png)

13. Checked which port is used by NFS and opend it using Security Groups (adding a new Inbound Rule):

![image](https://user-images.githubusercontent.com/58337007/153351403-f2e3a6ba-0b0c-4bbe-b6da-e0734c2f6df0.png)




# STEP 2 - PREPARED DATABASE SERVER:

1. Ran the cmdlet below to install MySQL server in the DB Server:

`sudo apt install mysql-server -y`

![image](https://user-images.githubusercontent.com/58337007/153344844-ba65829a-9e20-4552-8558-545ac9970482.png)



2. Created a database and named it 'tooling' as follows:

`sudo mysql'

![image](https://user-images.githubusercontent.com/58337007/153345224-5d0570c7-78d5-4f19-9172-af7c5ca00fd6.png)

3. Created database user named 'webaccess' by running the following command:

`create user 'webaccess'@'172.31.80.0/20' identified by 'password';`

![image](https://user-images.githubusercontent.com/58337007/153346307-c3346dda-5af1-46a9-9f01-36d7c3bb7542.png)

4. Granted permission to webaccess user on tooling database to do anything only from the webservers subnet cidr using the cmdlet below:

`grant all privileges on tooling.* to 'webaccess'@'172.31.80.0/20';`

`flush privileges;`

`show databases;`

![image](https://user-images.githubusercontent.com/58337007/153347027-9b03932d-a754-4f71-ab6c-bc7bb4ca7053.png)

# STEP 3 - PREPARED WEBSERVERS:

1. Connected into the WEB 1, WEB2, and WEB3 servers via SSH.

2. Installed NFS client in all 3 Web Servers using the cmdlet below:

`sudo yum install nfs-utils nfs4-acl-tools -y`

![image](https://user-images.githubusercontent.com/58337007/153352570-238406f4-405f-4d9e-9d7c-3691db846073.png)

3. Mounted /var/www/ and targeted the NFS server's export for apps using the cmdlets below:

`sudo mkdir /var/www/`

`sudo mount -t nfs -o rw,nosuid 172.31.87.158:/mnt/apps /var/www`

![image](https://user-images.githubusercontent.com/58337007/153353558-444fa691-a759-418f-a225-03fdd771f9af.png)

4. Verified that NFS was monunted successfully by running df -h. Made sure that the changes will persist on Web Server after reboot, using the cmdlets below:

![image](https://user-images.githubusercontent.com/58337007/153354740-c8defa16-4ef2-451f-a81e-c7c9fc4e2ba4.png)

`sudo vi /etc/fstab`

added the following line of code:

`172.31.87.158:/mnt/apps /var/www nfs defaults 0 0`

![image](https://user-images.githubusercontent.com/58337007/153355516-eb56bc28-324d-45f0-aee7-79e810816711.png)

5. Installed Remi's repository, Apache and PHP using the cmdlets below:

`sudo yum install httpd -y`

![image](https://user-images.githubusercontent.com/58337007/153356607-556c85c1-dff6-4d5f-9ab8-a7f2e5fa8bfa.png)

`sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

![image](https://user-images.githubusercontent.com/58337007/153363071-1a80590e-efe1-49ce-bf3e-0abaec0defcb.png)

`sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

![image](https://user-images.githubusercontent.com/58337007/153363515-6d3fe7ee-83de-4fde-954e-48a55a41bd92.png)

`sudo dnf module reset php`

![image](https://user-images.githubusercontent.com/58337007/153364183-769bc3e4-de9c-48fe-bbc1-3da6e6fa4709.png)

`sudo dnf module enable php:remi-7.4`

![image](https://user-images.githubusercontent.com/58337007/153365078-c4bef8bd-e995-4bfe-9661-c0d00aad167b.png)

`sudo dnf install php php-opcache php-gd php-curl php-mysqlnd`

![image](https://user-images.githubusercontent.com/58337007/153365301-feea1ecd-8a5c-4770-b511-85638f2d4d1b.png)

`sudo systemctl start php-fpm`

`sudo systemctl enable php-fpm`

![image](https://user-images.githubusercontent.com/58337007/153365540-a0236c3f-d4bb-4971-af8f-0ea9325b9a46.png)

`setsebool -P httpd_execmem 1`

6. Verified that Apache files and directories are available on the Web Server in /var/www and also NFS server in /mnt/apps. Seeing same files means NFS is mounted correctly. 

![image](https://user-images.githubusercontent.com/58337007/153357738-316ff49d-cb1f-4866-99fd-0cb3010cf820.png)

7. Located the log folder for Apache on the Web Server and mounted it to NFS server's export for log. Repeated step No4 to make sure the mount point will persist after reboot.

8. Forked the tooling source code from Darey.io Github Account by running the cmdlets below:

`sudo yum install git -y`

![image](https://user-images.githubusercontent.com/58337007/153360488-bcdb596b-a4c0-4d69-b5d1-ef0c0ebb9d07.png)

Initialized an empty git repository:

`git init`

`git clone https://github.com/darey-io/tooling.git`

![image](https://user-images.githubusercontent.com/58337007/153361178-a2ae8900-53f7-4de6-b6ec-a61421b71a12.png)

9. Deployed the tooling website's code to the Webserver and ensured that the html folder from the repository is deployed to /var/www/html using the cmdlet below:

`sudo CP -R html/. /var/www/html`

![image](https://user-images.githubusercontent.com/58337007/153362268-b88ff6b1-8659-4af1-bf7c-167c663c9f66.png)

Checked the Webserver public IP address is reachable over the web:

![image](https://user-images.githubusercontent.com/58337007/153367682-ff8ad5f0-97e1-4688-9f67-e9de71ed3e4e.png)

![image](https://user-images.githubusercontent.com/58337007/153367754-3bd4d6bb-0ca5-4d63-a8fc-efb92a4854f9.png)

10. Updated the website's configuration to connecto to the database(in /var/www/html/functions.php file). 

![image](https://user-images.githubusercontent.com/58337007/153369567-3f4bdf85-351f-4479-ac46-659a49b1a5ff.png)

Updated the mysql config file using the cmmdlet below:

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf` changing the bind addresses to 0.0.0.0

![image](https://user-images.githubusercontent.com/58337007/153374017-3e050f0b-347d-4a73-bf91-82c8bd6a0a1b.png)

Connected to the DB Server mysql using the cmdlet below:

`mysql -h 172.31.93.133 -u webaccess -p tooling < tooling-db.sql`

![image](https://user-images.githubusercontent.com/58337007/153374998-bce341bd-d436-4865-916d-d9c29fe9a23b.png)

11. Created in MySQL a new admin user with username: myuser and password: password, as follows:

`INSERT INTO ‘users’ (id, username, password, email, user_type, status) VALUES
-> (2, ‘myuser’, ‘password’, ‘user@mail.com’, ‘admin’, ‘1’);`

![image](https://user-images.githubusercontent.com/58337007/153381641-698c7e9a-ba78-496d-8dea-e02ce64c403d.png)

12. Opened the website in a browswer and logged in with the newly created 'myuser' user.

![image](https://user-images.githubusercontent.com/58337007/153382918-2364e5a0-ab5b-4688-96ae-824ad5e84751.png)






