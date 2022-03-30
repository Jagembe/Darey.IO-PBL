## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

#### Step 1 - Jenkins job enhancement

1. Created a new directory called *ansible-config-artifact* in the Jenkins-Ansible server to store all artifacts after each build using the cmdlet below:
`sudo mkdir /home/ubuntu/ansible-config-artifact`
![image](https://user-images.githubusercontent.com/58337007/160481184-ae86a26a-7ecf-4c2c-bf57-1345a542c61e.png)

2. Changed permissions to this directory, so Jenkins could save files there - `chmod -R 0777 /home/ubuntu/ansible-config-artifact`
![image](https://user-images.githubusercontent.com/58337007/160481569-76719ea9-2006-4045-ab4f-6d518c5e9a6e.png)

3. Accessed the Jenkins web console as follows: -> Manage Jenkins -> Manage Plugins -> on Available tab searched for *Copy Artifact* and installed the plugin without restarting Jenkins.
![image](https://user-images.githubusercontent.com/58337007/160482885-e447f491-da3e-41c4-b2d5-032a24c14a58.png)
![image](https://user-images.githubusercontent.com/58337007/160483101-ae2bbbce-81c3-4178-a1ef-0ec91a9f38bb.png)

4. Created a new Freestyle project and named it *save_artifacts*.
![image](https://user-images.githubusercontent.com/58337007/160486354-d7a1bdeb-44ea-4a10-a1af-54e9da98e845.png)

5. Configured the existing *ansible* project as follows:
![image](https://user-images.githubusercontent.com/58337007/160487227-64c87301-5164-4201-a16a-083f0b656170.png)

6. Created a *Build* step and chose *Copy artifacts from other project*, specified ansible as a source project and set * /home/ubuntu/ansible-config-artifact* as a target directory.
![image](https://user-images.githubusercontent.com/58337007/160487227-64c87301-5164-4201-a16a-083f0b656170.png)

7. Tested my set up by making some change in README.MD file inside my *ansible-config-mgt* repository (right inside *main* branch). If both Jenkins jobs have completed one after another - I shall see my files inside */home/ubuntu/ansible-config-artifact* directory and it will be updated with every commit to my *main* branch.
![image](https://user-images.githubusercontent.com/58337007/160488205-29d941e3-334a-4adc-98b1-bd32ee9d6dc1.png)
![image](https://user-images.githubusercontent.com/58337007/160488282-12e5a019-6757-4eac-8fb5-19b4620202c1.png)

## REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML

#### Step 2 – Refactor Ansible code by importing other playbooks into *site.yml*

Before starting to refactor the codes, I pulled down the latest code from master (main) branch, and created a new branch, named it *refactor*.
![image](https://user-images.githubusercontent.com/58337007/160719307-61bc6901-02f0-4928-b912-cde7bc1c3198.png)

DevOps philosophy implies constant iterative improvement for better efficiency – refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In Project 11 I wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and I need to apply this playbook to other servers with different requirements. In this case, I will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that I need to add for certain server/OS families. Very fast it will become a tedious exercise and my playbook will become messy with many commented parts. My DevOps colleagues will not appreciate such organization of my codes and it will be difficult for them to use my playbook.

Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

Let see code re-use in action by importing other playbooks.

1. Within playbooks folder, created a new file and named it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that I created previously.
![image](https://user-images.githubusercontent.com/58337007/160491692-1c49d075-57ce-4de6-8d18-4b2a93a96a54.png)

2. Created a new folder in root of the repository and named it *static-assignments*. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of my work. It is not an Ansible specific concept, therefore I can choose how I want to organize my work. You will see why the folder name has a prefix of static very soon. For now, just follow along.
![image](https://user-images.githubusercontent.com/58337007/160492880-d8579cfd-62ce-4950-bdf2-f8d453d870dc.png)

3. Moved common.yml file into the newly created static-assignments folder.
![image](https://user-images.githubusercontent.com/58337007/160496359-c78f7629-4d12-409c-bf4a-8c61dc72b674.png)

4. Inside site.yml file, imported common.yml playbook.

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```

![image](https://user-images.githubusercontent.com/58337007/160497066-8c38c1a2-206a-480a-9e67-28393a4ef9d0.png)
![image](https://user-images.githubusercontent.com/58337007/160497344-d7332756-3d43-41e4-a797-0b504cfb83af.png)

My folder structure looks like this:
![image](https://user-images.githubusercontent.com/58337007/160497829-1ddfab7e-1127-4739-8576-ad50cecf305d.png)
![image](https://user-images.githubusercontent.com/58337007/160497589-d4e95cb1-62a9-4781-8143-91b507685793.png)

5. Ran ansible-playbook command against the dev environment

Since I need to apply some tasks to my dev servers and wireshark is already installed – I can go ahead and create another playbook under static-assignments and name it *common-del.yml*. In this playbook, configured deletion of wireshark utility.

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```
![image](https://user-images.githubusercontent.com/58337007/160498707-01c5d040-9d0c-4865-92c2-61f067b3135c.png)
![image](https://user-images.githubusercontent.com/58337007/160499382-cd071a4f-138d-4ce6-ba9b-6de1a6d073a2.png)

Updated *site.yml* with - `import_playbook: ../static-assignments/common-del.yml` instead of *common.yml* and ran it against dev servers:

![image](https://user-images.githubusercontent.com/58337007/160540216-b698ffc6-2e58-49d5-bf80-9d664659a64f.png)

`sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/dev.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml`

Made sure that wireshark is deleted on all the servers by running the following cmdlet:

`wireshark --version`

Ran the ansible playbook

![image](https://user-images.githubusercontent.com/58337007/160716092-e59f2768-9067-4c9d-bfa4-b514a7ff4df1.png)

SSH into the Webserver 1 (jenkins node) and ran the following command to check if ansible playbook performed task/s as expected.
`which wireshark`
![image](https://user-images.githubusercontent.com/58337007/160717144-a01bb65d-6322-45cd-9d60-52fd2ae61d99.png)
![image](https://user-images.githubusercontent.com/58337007/160718915-feb2dca6-a1dd-49f8-a29d-131051d1afc2.png)


## CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER’

#### Step 3 – Configure UAT Webservers with a role ‘Webserver’

I have a nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. I could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, I will use a dedicated role to make my configuration reusable.

1. Launched 2 fresh EC2 instances using RHEL 8 image, I will use them as my uat servers, so give them names accordingly – *Web1-UAT* and *Web2-UAT*.
Tip: Do not forget to stop EC2 instances that you are not using at the moment to avoid paying extra. For now, you only need 2 new RHEL 8 servers as Web Servers and 1 existing Jenkins-Ansible server up and running.
![image](https://user-images.githubusercontent.com/58337007/160721759-8b485b33-3e7f-41fa-818d-463e06a05c20.png)

2. To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

There are two ways how you can create this folder structure:

* Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)
```
mkdir roles
cd roles
ansible-galaxy init webserver
```
* Create the directory/files structure manually

Note: I am electing to create them manually since I store all of my codes in GitHub and will create them there rather than locally on the Jenkins-Ansible server.

My roles structure looks like this:
![image](https://user-images.githubusercontent.com/58337007/160724177-9ca8fd8e-7720-4f97-8ca7-b77cbd586866.png)

3. Updated my inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of my 2 UAT Web servers as follows:

```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>
<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>
```
![image](https://user-images.githubusercontent.com/58337007/160726333-23e6f3d9-9f6d-4326-9447-25c8ea64e7cd.png)
**Note** - Since we had used ssh-agent to connect to our servers, we do not need to provide the private key file path.

4. In */etc/ansible/ansible.cfg* file uncommented *roles_path* string and provided a full path to my roles directory *roles_path   = /home/ubuntu/ansible-config-artifact/roles*, so Ansible could know where to find configured roles.
![image](https://user-images.githubusercontent.com/58337007/160727736-b62bb0e7-55a4-402d-8acb-f5db00aa11f5.png)

5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

* Install and configure Apache (httpd service)
* Clone **Tooling website** from GitHub https://github.com/<your-name>/tooling.git.
* Ensure the *tooling* website code is deployed to /var/www/html on each of 2 UAT Web servers.
* Make sure *httpd* service is started

My main.yml may consist of the following tasks:

```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```
![image](https://user-images.githubusercontent.com/58337007/160728546-1cce3d5d-077d-48c1-b925-f755628413f4.png)

## REFERENCE WEBSERVER ROLE

#### Step 4 - Reference 'Webserver' role

Within the *static-assignments* folder, created a new assignment for **uat-webservers** *uat-webservers.yml*. This is where I will reference the role.
```
---
- hosts: uat-webservers
  roles:
     - webserver
```
![image](https://user-images.githubusercontent.com/58337007/160729956-c013f738-1ad9-454e-8039-737dd0348de3.png)

The entry point to our ansible configuration is the *site.yml* file. Therefore, I will refer to my *uat-webservers.yml* role inside *site.yml*.

I should have this in *site.yml*

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml
```
![image](https://user-images.githubusercontent.com/58337007/160730936-b8341900-38c8-4dea-afbe-4fac14c5c231.png)

#### Step 5 – Commit & Test
Commit your changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.
![image](https://user-images.githubusercontent.com/58337007/160731627-8254411f-e41a-4435-9852-a157d3894c4e.png)

Now run the playbook against your uat inventory and see what happens:
```
sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml
```
![image](https://user-images.githubusercontent.com/58337007/160925182-3efca250-6ad9-4200-b166-97ce0389cae1.png)


We see both the UAT Web servers configured and can try to reach them from my browser as follows:

* *http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php*
![image](https://user-images.githubusercontent.com/58337007/160927055-cf26b93f-10f1-4b99-8ffc-95c89f559eff.png)

or 

* *http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php*
![image](https://user-images.githubusercontent.com/58337007/160926816-fa4b6319-7cad-4ec1-b9e5-9fe6dc54e96a.png)