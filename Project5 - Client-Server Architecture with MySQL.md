# PROJECT 5: CLIENT-SERVER ARCHITECTURE WITH MYSQL
Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another. In their communication, each machine has its own role: the machine sending requests is usually referred to as "Client" and the machine responding  (serving) is called "Server".

## Implementing a Client-Server Architecture using MySQL Database Management System (DBMS).
The following steps will demonstrate a basic client-server using MySQL Relational Database Management System (RDMS).

1. Created and configured two Linux-based virtual servers (EC2 instances in AWS).

```
Server A name - `DB-Server`
Server B name - `Client`
```
 2. Updated and Installed MySQL software in the `DB-Server` using the follwoing cmdlet:
 
 `sudo apt update -y`
 
 Output of the above command.
 
 ![image](https://user-images.githubusercontent.com/58337007/151894035-8a7d2e10-c6ca-4242-b8fc-8aa2afbcd1e1.png)
 
 `sudo apt install mysql-server -y`
 
 Output of the above command.
 
 ![image](https://user-images.githubusercontent.com/58337007/151894590-08b1576f-d120-450f-a590-a9d7ba163aec.png)
 
 Enabled the MySQL service in the 'DB-Server' by running the following cmdlet:
 
 `sudo systemctl enable mysql`
 
 Output of the above command.
 
 ![image](https://user-images.githubusercontent.com/58337007/151895095-b9cfb5ab-bc52-4f84-b265-9f3c2d809c00.png)
 
 3. SSH'ed to the 'Client' (server B), updated and installed the MySQL Client software using the following cmdlet:
 
 `sudo apt update -y`
 
 Output of the above command.
 
 ![image](https://user-images.githubusercontent.com/58337007/151895712-8937241f-e308-4701-b807-daabd590f72d.png)
 
 `sudo apt install mysql-client -y`
 
 Output of the above command.
 
 ![image](https://user-images.githubusercontent.com/58337007/151895973-84673cfa-40b5-4e9d-9568-dec1e6fa573e.png)

4. Allowed connection on port 3306 by updating the Inbound security group rules of the 'DB-Server' so that it can receive instructions from the 'Client' as follows:

Output of the above step.

![image](https://user-images.githubusercontent.com/58337007/151905329-53440a54-2f3c-48af-8f0d-77402f932f19.png)

Configured mySQL 'DB-Server' for use, user creation and security as such:

`sudo mysql_secure_installation`

Output of the above command.

![image](https://user-images.githubusercontent.com/58337007/151909779-25ea7405-3de7-4829-8476-2a302f5c8b4a.png)

Authenticated into MySQL in the 'DB-Server' using the cmdlet below:

`sudo mysql`

![image](https://user-images.githubusercontent.com/58337007/151910163-f68fdce9-aace-4a01-b49d-106663b9f0b0.png)

Created a 'remote_user' in mysql using the following command:

`CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

Output of the above command.

![image](https://user-images.githubusercontent.com/58337007/151910863-1097d977-d197-44c3-823a-fab96139ee1d.png)

Created a database in mysql using the following command:

`CREATE DATABASE test_db;`

Output of the above command.

![image](https://user-images.githubusercontent.com/58337007/151911099-c7b310a1-303c-46e5-895d-3bf1cc5018ba.png)

Granted the remote user all permissions to the 'test_db' using the script below:

GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;

Output of the above command.

![image](https://user-images.githubusercontent.com/58337007/151911351-375f3b2b-130f-4298-afde-0a74a6af0f32.png)

Finally, flushed privileges to tell the server to reload the grant tables using the 'FLUSH PRIVILEGES' statement:

`FLUSH PRIVILEGES;`

Output of the above command

![image](https://user-images.githubusercontent.com/58337007/151911699-28db227a-b0bd-404d-b173-3589e603adb3.png)

Configured MySQL to allow connections from remote hosts using the following cmdlet ahd updated the mysql config file:

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

Updated the 'bind address' with 0.0.0.0

![image](https://user-images.githubusercontent.com/58337007/151912422-f9fe9e02-14f2-4b31-a0bd-be5c87e44f6b.png)

Restarted mysql services after updating the mysql configuration file using the cmdlet below:

`sudo systemctl restart mysql`

Output of the command above.

![image](https://user-images.githubusercontent.com/58337007/151912645-181eef1e-c718-43b2-82fa-121ee6fd8615.png)

Connected the 'Client' to the 'DB-Server' using the cmdlet below:

`sudo mysql -u remote_user -h 172.31.35.174 -p`

Output of the above command.

![image](https://user-images.githubusercontent.com/58337007/151913260-0c24d000-03a4-41bc-92f1-79520a6d5b86.png)

Tested connection and access to the DB-Server by running the script below:

`Show databases;`

Output of the above statement.

![image](https://user-images.githubusercontent.com/58337007/151913472-5e4aba2c-f55e-4a2e-bd5b-1276c2c57c51.png)

Created a table 'projects' in the test_db database and added data to it using the sql statements below:

```
CREATE TABLE projects(
	project_id INT AUTO_INCREMENT, 
	name VARCHAR(100) NOT NULL,
	start_date DATE,
	end_date DATE,
	PRIMARY KEY(project_id)
);



INSERT INTO 
	projects(name, start_date, end_date)
VALUES
	('AI for Kubernetes','2022-02-01','2022-05-31'),
	('Python for Automation','2022-06-01','2022-09-30');
 ```
 
 Output of the above statement.
 
![image](https://user-images.githubusercontent.com/58337007/151915028-d23553af-fa62-4514-835c-80a78f43f28d.png)
![image](https://user-images.githubusercontent.com/58337007/151914998-adf529ce-6b5f-4c9f-893d-8bcd99ac0934.png)






