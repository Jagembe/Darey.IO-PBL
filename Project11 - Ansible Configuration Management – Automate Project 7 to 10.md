# PROJECT 11: Ansible – Automate Project 7 to 10
#### Install and configure Ansible on EC2 Instance

1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
![image](https://user-images.githubusercontent.com/58337007/158902278-791e2fab-57b0-4fd9-8dcc-8333360b20c5.png)

2. In my GitHub account, created a new repository and name it ansible-config-mgt.
![image](https://user-images.githubusercontent.com/58337007/158902822-144a38e1-9be7-4176-bea5-01e42e79734c.png)

SSH-d into the Jenkins-Ansible server.

3. Installed Ansible by running the following cmdlet on the Jenkins-Ansible server:
```
sudo apt update

sudo apt install ansible

Check your Ansible version by running ansible --version
```
![image](https://user-images.githubusercontent.com/58337007/158908075-115ba5c4-a8ea-4fbc-9a10-1db0ff78c9d5.png)

![image](https://user-images.githubusercontent.com/58337007/158908251-9b8eab9a-7be5-4a34-844a-7d48e9c180b7.png)

4. Configured Jenkins build job to save my repository content every time I change it.
  * Created a new Freestyle project ansible in Jenkins and pointed it to my ‘ansible-config-mgt’ repository.
![image](https://user-images.githubusercontent.com/58337007/158912076-2c8ac579-8fc3-41f6-8d7d-c2501f66d50a.png)
  * Configured Webhook in GitHub and set webhook to trigger ansible build.
![image](https://user-images.githubusercontent.com/58337007/158912402-1cedabb5-f181-46ed-90d4-36f951fb63a5.png)
  * Configured a Post-build job to save all **(\**\)** files, like I did in Project 9.
![image](https://user-images.githubusercontent.com/58337007/158912644-06054c8c-88d5-4727-8a16-c3f4b645c7f1.png)

5. Tested my setup by making some changes in README.MD file in **MAIN** branch and made sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder.
![image](https://user-images.githubusercontent.com/58337007/158914863-968e7e2e-d7e9-4460-bc8a-60f908d560d0.png)
![image](https://user-images.githubusercontent.com/58337007/158915547-ce687a78-8803-4009-8666-a78d1f0d559f.png)
![image](https://user-images.githubusercontent.com/58337007/158915868-4432ad44-da34-4945-852e-178685d684f1.png)
ls /var/lib/jenkins/jobs/ansible/builds/4/archive/

###### Step 2 – Prepared my development environment using Visual Studio Code
Successfully installed Visual Studio Code (VSC) and configured it to connect to my newly created GitHub repository.

##### Begin Ansible Development
1. In my ansible-config-mgt GitHub repository, I created a new branch that will be used for development of a new feature.

'prj-11'

2. Checked out the newly created feature branch to my local machine and started building my code and directory structure
![image](https://user-images.githubusercontent.com/58337007/158931410-bec35e3b-e53a-44da-b2a0-4c1e8da28a85.png)
![image](https://user-images.githubusercontent.com/58337007/158931940-240be8fd-4dd9-4016-9d53-784f52a9fc09.png)

3. Created a directory and named it playbooks – it will be used to store all my playbook files.
![image](https://user-images.githubusercontent.com/58337007/158932216-7aad3e44-3594-446e-ab49-79bb3f03fdf7.png)
![image](https://user-images.githubusercontent.com/58337007/158932354-52e53302-5189-49ce-b2ef-9b6c132d971c.png)

4. Created a directory and named it inventory – it will be used to keep my hosts organised.
![image](https://user-images.githubusercontent.com/58337007/158932622-52f603f6-2890-463f-9587-001fd734dc0e.png)
![image](https://user-images.githubusercontent.com/58337007/158932747-c87417e6-0fbb-4580-a7a6-b820e595c652.png)

5. Within the playbooks folder, created my first playbook, and named it common.yml
![image](https://user-images.githubusercontent.com/58337007/158932892-981b304d-b912-4d25-9ec6-4284ba0bcb3b.png)

6. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
![image](https://user-images.githubusercontent.com/58337007/158933164-daf386b7-5bdc-49ca-af09-85328667c385.png)

#### Step 4 – Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Saved below inventory structure in the inventory/dev file to start configuring my development servers. Ensured to replace the IP addresses according to my own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this I can implement the concept of ssh-agent. Now I need to import my key into ssh-agent using the cmdlets below:

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```
![image](https://user-images.githubusercontent.com/58337007/158933932-c990ef28-4832-429e-b850-fa19f24352b4.png)


Confirm the key has been added with the command below, you should see the name of your key

ssh-add -l
![image](https://user-images.githubusercontent.com/58337007/158934473-447a04c0-a690-43d8-9976-77c0b943a8aa.png)

Now, ssh into your Jenkins-Ansible server using ssh-agent

ssh -A ubuntu@public-ip
![image](https://user-images.githubusercontent.com/58337007/158934237-56fd2634-bc2a-4eba-a583-4e8c6cab4882.png)

Also noticed, that my Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

Update my inventory/dev.yml file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```
![image](https://user-images.githubusercontent.com/58337007/158936528-7d0349fc-26fd-46b7-89e4-3fca99e36d96.png)


#### Created a Common Playbook
##### Step 5 – Create a Common Playbook

It is time to start giving Ansible the instructions on what needs to be performed on all servers listed in inventory/dev.

In common.yml playbook I will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Updated my playbooks/common.yml file with following code:

```
---
- name: update web and nfs servers
  hosts: webservers, nfs
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb, db
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```
![image](https://user-images.githubusercontent.com/58337007/158937418-82011851-0932-43a0-87ee-52aa1653debc.png)

Examined the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

    Create a directory and a file inside it
    Change timezone on all servers
    Run some shell script
    …

For a better understanding of Ansible playbooks – watch this video from RedHat and read this article.
Step 6 – Update GIT with the latest code

Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

    use git commands to add, commit and push your branch to GitHub.

git status
![image](https://user-images.githubusercontent.com/58337007/158938115-cffd8b5c-c51e-4991-bb25-06e4d88eb2b0.png)

git add .

git commit -m "commit message"
![image](https://user-images.githubusercontent.com/58337007/158939466-79cf5d9c-e65e-4c7f-b395-dcb6b7095228.png)
![image](https://user-images.githubusercontent.com/58337007/158939672-ff7a079f-7a64-475e-9558-1051cfe1ff34.png)

Create a Pull request (PR)
![image](https://user-images.githubusercontent.com/58337007/158939858-8e818a72-08ea-4be8-be6a-248097cb800a.png)

Wear a hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the master branch.
![image](https://user-images.githubusercontent.com/58337007/158940041-f31efb2e-2869-45d7-8ac1-6b09775ea2ff.png)
![image](https://user-images.githubusercontent.com/58337007/158940153-b063180e-e89d-4d93-a0e1-782bdde59206.png)
![image](https://user-images.githubusercontent.com/58337007/158940568-54be3bff-c34f-4c85-843f-6f82b413a563.png)

Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.
![image](https://user-images.githubusercontent.com/58337007/158941270-f4e745cb-a97a-4b60-90a7-a53eb3a77093.png)

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.
![image](https://user-images.githubusercontent.com/58337007/158940907-2c3c53ce-a84a-4fc7-bab9-c349b38ca668.png)

#### Run first Ansible test
##### Step 7 – Run first Ansible test

Now, it is time to execute ansible-playbook command and verify if that playbook actually works:

connected to the jenkins-ansible server via VSCode;
![image](https://user-images.githubusercontent.com/58337007/158942765-ae2c3e62-f49e-4679-a135-b7cc98ded427.png)

Opened a folder on (home directory) on VSCode
![image](https://user-images.githubusercontent.com/58337007/158943008-0f8ac096-02c1-4859-be55-723d53128a97.png)
![image](https://user-images.githubusercontent.com/58337007/158943102-d6a89fea-1034-4c45-8e03-30eefd5dc89d.png)

`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/7/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/7/archive/playbooks/common.yml`
![image](https://user-images.githubusercontent.com/58337007/159839281-2342080a-9d8b-4841-8a40-cd6f60d014f7.png)

Note: Previous command we ran without sudo, this is because we had added an ssh key to ssh-agent for our regular user. If you try to run this command with sudo you will have to explicitly pass the ssh key with --private-key <path-to-private-key> parameter.

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

 Connected (via ssh) to webserver 1 (RHEL):
![image](https://user-images.githubusercontent.com/58337007/159839403-59d506f2-d53f-4f0a-be01-a19bb4bbe554.png)
 
 Connected (via ssh) to load balancer (ubuntu):
![image](https://user-images.githubusercontent.com/58337007/159839796-985a4534-8f21-4c7e-b9f3-4a7fd812a854.png)
 
Ran ansible playbook with updated tasks as follows:
`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/18/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/18/archive/playbooks/common.yml`
 
 ![image](https://user-images.githubusercontent.com/58337007/160120867-1424090f-505d-4bce-b72e-0edbcba05732.png)
 
![image](https://user-images.githubusercontent.com/58337007/160121029-46dcaa1b-0fc4-4d34-be79-781d823af7db.png)

 
 

 


