# AUX PROJECT 1: SHELL SCRIPTING
## For this project, I launched an EC2 Ubuntu Server on AWS where I will be creating a Users Group and adding 20 users to the group.
### STEPS:
Used the following cmdlet to create an 'onborad.sh' file in my local Downloads folder where my '.pem' AWS key resides.

`vi onboard.sh`

Copied the file to my ubuntu root directory using the cmdlet below:

`$ scp -i my-private-key-nv.pem onboard.sh ubuntu@3.94.162.62:~/;`

Output of above command

![image](https://user-images.githubusercontent.com/58337007/151683817-c4b1525b-4ce6-4a35-86af-b8898b4fdffa.png)

Connected to the remote server (ubuntu on AWS) to ensure the copied 'onboard.sh file is in there.

Used the cmdlet below to check the existence of the file in the ubuntu server.

`ls -l`

Output of the above command

![image](https://user-images.githubusercontent.com/58337007/151683917-912b2180-1504-4e1a-857c-a6c0ba3d4e3d.png)

Created a directory called 'Shell' in the root folder of the remote Ubuntu server on AWS using the cmdlet below:

`mkdir Shell`

Used the cmdlet below to move the 'onboard.sh' file to the 'Shell' directory

'mv onboard.sh /home/ubuntu/shell/`

Changed Directory (cd) into the directory 'Shell' using the cmdlet below:

`cd Shell`

![image](https://user-images.githubusercontent.com/58337007/151684055-952b8672-8d7a-415a-8d98-7e2e9a47678a.png)

Created the following files ('id_rsa', 'id_rsa.pub', 'names.csv') using the cmdlet below:

`touch  id_rsa id_rsa.pub names.csv`

Output of the above command

![image](https://user-images.githubusercontent.com/58337007/151684279-c4eea7a8-0bec-4b96-ad7e-eb3cde2988b7.png)

Used the following cmdlet to update the id_rsa.pub file first:

`vi id_rsa.pub`

Used the following cmdlet to update the id_rsa file with the private key:

`vi id_rsa`

Output of the above command:

![image](https://user-images.githubusercontent.com/58337007/151684435-6c255b7d-ebbb-4305-8fe1-49869a673662.png)

Used the cmdlet below to update the 'names.csv' file with 20 random representative of would be users being added to the ubuntu server:

`vi names.csv`

Output of the command above.

![image](https://user-images.githubusercontent.com/58337007/151684607-77b180c4-8283-4183-bfa5-4f11922d5c95.png)

Updated the 'onboard.sh' file using the cmdlet below:

`vi onboard.sh`

Output of the action above

![image](https://user-images.githubusercontent.com/58337007/151684705-62a9635c-0f71-48c4-8528-75fc849cedfa.png)

Added the follwing group to the 'Shell' directory in ubuntu server using the cmdlet below:

`sudo groupadd developers`

Used the cmdlet below to make the 'onboard.sh' file executable:

`sudo chmod +x onboard.sh`

Switched user from regular to 'Super user' (root user) using the cmdlet below in order to create new users:

`sudo su`

Output of above action

![image](https://user-images.githubusercontent.com/58337007/151684894-c3cfcb14-d906-4c8c-8421-42a3c95657c3.png)

Created new users (20) in ubuntu server by running the cmdlet below:

`./onboard.sh`

Output of the above action

![image](https://user-images.githubusercontent.com/58337007/151685639-8d19dbfa-d391-46ff-8163-55aa50d5c757.png)

Used the following cmdlet to check/verify the user creation along with user directories in the 'home' directory:

`ls -l /home/`

Output of the action above:

![image](https://user-images.githubusercontent.com/58337007/151685779-6deca1f1-f330-4455-99a4-d1e4935edcde.png)

Used the cmdlet below to chaeck that the developers group was created:

`getent group developers`

Output of the above action

![image](https://user-images.githubusercontent.com/58337007/151685820-2d4b02e3-c0a9-4177-b745-e89be611f342.png)

Used the cmdlet below to again check user creation and respective group:

`cat /etc/passwd`

Output of the above command

![image](https://user-images.githubusercontent.com/58337007/151685873-9a20e4c9-91fe-4e28-b4ae-dbc09c586115.png)

Used the 'awk' command to list users and groups they belong to:

`cat /etc/passwd | awk -F':' '{ print $1}' | xargs -n1 groups`

Output of the action above:

![image](https://user-images.githubusercontent.com/58337007/151686229-49ee7bcf-c4ef-4a3a-bb5d-87fd685b18ec.png)

Finally tested a randomly selected user of the newly created group 'developers' for access to the server as follows:

Using a new terminal session in the local machine, emulated user Damian connecting to the remote server as follows by creating a .pem file that will hold the private id_rsa key:

`vi aux-proj.pem`

Pasted the private key from the id_rsa file in the 'aux-proj.pem file.

Used the following cmdlet to login to the ubuntu server as user Damian:

`ssh -i aux-proj.pem Damian@3.94.162.62`

Output of the above command

![image](https://user-images.githubusercontent.com/58337007/151686544-c91a0be2-2169-4e77-b0d0-20fb302cd307.png)

Checked user Damian persmission settings by running the following cmdlet:

`sudo apt update`

Output of the following action:

![image](https://user-images.githubusercontent.com/58337007/151686606-4ca1b021-c468-4edb-a020-ef5f190b0e19.png)

User Damian is not in the sudoers file - meaning he has no admin privileges, and the incident will be reported.

Connected as another user 'Tracy'

![image](https://user-images.githubusercontent.com/58337007/151686719-c3015e8f-c4b9-4e6f-88f2-f431a5f8384d.png)

While connected as user Tracy , was able to cat into the authorized keys file in the .ssh folder:

![image](https://user-images.githubusercontent.com/58337007/151686775-b7cc41ce-c0a6-4782-9958-60936cd5d1f4.png)







