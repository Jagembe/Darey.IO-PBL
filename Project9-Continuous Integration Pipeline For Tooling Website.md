# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION - INTRODUCTION TO JENKINS

This is a continuation of Project 8, where we deployed a load balancer (Horizontal Scaling) where we added new Web Servers to the Tooling Webite and used the load balancer to distribute traffic between them.
In Project 9, I am going to start automation of the routine tasks using open source automation server - Jenkins. When done, our updated architecture will look like this:

![image](https://user-images.githubusercontent.com/58337007/154813856-7038de99-0919-4880-8aea-7cafa37809e9.png)

### INSTALL AND CONFIGURE JENKINS SERVER

##### Step 1 - Install Jenkins server

1. Launched an AWS EC2 server based on Ubuntu Server 20.04 LTS and named it 'Jenkins'.

2. Installed JDK in the 'Jenkins' server (Jenkins is a Java-based application) using the cmdlet below:

```
sudo apt update

sudo apt install default-jdk-headless
```

![image](https://user-images.githubusercontent.com/58337007/154814868-9931c3ab-f34e-4522-887c-ebcab6f66454.png)

3. Installed Jenkins using the cmdlets below:

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

![image](https://user-images.githubusercontent.com/58337007/154815038-5ab65267-6dec-419d-8b5f-bb27c66241db.png)

Checked and ensured Jenkins is up and running using the cmdlet below:

`sudo systemctl status jenkins`

![image](https://user-images.githubusercontent.com/58337007/154815100-1e429713-5e84-4dd2-9d92-fa1f6be9db9d.png)

4. By default Jenkins server uses TCP port 8080 - Opened it by creating a new Inbound Rule in the EC2 Security Group attached to the Jenkins Server.

![image](https://user-images.githubusercontent.com/58337007/154815208-172662c3-bee4-4a74-b347-f164687c54a7.png)

Accessed the Jenkins web server over the internet using the public IP address and the port 8080.

![image](https://user-images.githubusercontent.com/58337007/154815290-145057f8-3db3-4718-b593-d7249f7053a2.png)

5. Performed initial Jenkins setup as follows:

  - Retrieved the Jenkis Administrator password from the server using the cmdlet below:
  `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
  ![image](https://user-images.githubusercontent.com/58337007/154815464-3b96a4e9-207e-4a2c-89c6-98d853dab1a9.png)
  - Pasted the password to the Jenkins web server page on the 'Admiistrator password' text box.
  ![image](https://user-images.githubusercontent.com/58337007/154815578-07535c1d-8715-4d00-a529-1f590aa2a965.png)
  - Installed suggested plugins
  ![image](https://user-images.githubusercontent.com/58337007/154815671-8e3c143a-849b-465c-b1b5-6bd16e819a76.png)
  ![image](https://user-images.githubusercontent.com/58337007/154815759-a9de6438-687a-4f52-a3f7-ac6d6eed1c3f.png)
  - Created an admin user and obtained my Jenkins server address.
  ![image](https://user-images.githubusercontent.com/58337007/154815913-4cff401d-716a-4746-9b63-3e067b43423e.png)
  - Jenkins is setup and ready for use.
  ![image](https://user-images.githubusercontent.com/58337007/154816110-456ca6ce-134a-4be4-bacb-a9a933deb011.png)
  
##### Step 2 - Configured Jenkins to retrieve source codes from GitHub using Webhooks
In this part, I configure a simple Jenkins job/project that will be triggered by GitHub 'webhooks' and will execute a 'build' task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enabled webhooks in my GitHub repository settings.
![image](https://user-images.githubusercontent.com/58337007/154816467-941bcac7-04fe-41e9-af94-08e364b5e770.png)

2. Accessed the Jenkins web console and clicked "New Item" and created a "Freestyle project".
![image](https://user-images.githubusercontent.com/58337007/154816589-f7d48272-f457-4875-b228-946c85b1c05f.png)

3.Connected to my GitHub repository, provided its URL.
![image](https://user-images.githubusercontent.com/58337007/154817040-91afb521-96c8-432d-a558-aee3fe6e1a84.png)

4. In configuration of my Jenkins freestyle project, I chose Git repository, provided the link to my Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
![image](https://user-images.githubusercontent.com/58337007/154817148-82c0f52c-1007-4ac3-a771-8f6d5a7a56c0.png)

5. Saved the configuration and tried to run the build, *manually* for now by clicking ont he "Build Now" button.
![image](https://user-images.githubusercontent.com/58337007/154817250-ab6e0330-611d-4601-a92c-69336fd1cb71.png)

Output of the first build
![image](https://user-images.githubusercontent.com/58337007/154817308-07da3499-d732-4efc-8776-c28553dc8e51.png)

Opened the build and checked in "Console Output" that it ran successfully. (This does not produce anything and only runs manually - need to fix this in the next steps).
![image](https://user-images.githubusercontent.com/58337007/154817435-1b5f9ec2-9aae-45af-b869-2e0f8aeb527e.png)

6. Clicked "Configure" on my project/job and added the following two (2) configurations:
  - Configured triggering the job from GitHub webhook:
  ![image](https://user-images.githubusercontent.com/58337007/154817630-b55dc443-a49c-45af-804c-36a4273e836c.png)
  - Configured "Post-build Actions" to archive all the files - files resulted from a build are called "artifacts".
  ![image](https://user-images.githubusercontent.com/58337007/154817720-02dbd109-0659-42d1-afa2-592d2f044852.png)

Made some changes to the "README" file in the tooling GitHub repository and pushed changes to the master branch.
![image](https://user-images.githubusercontent.com/58337007/154817881-3c7f54a3-b13a-4b59-bd38-4bbb71e26870.png)
![image](https://user-images.githubusercontent.com/58337007/154817978-4ea8026e-9a49-4a2b-8b24-5ed2a186e740.png)

Checked Jenkins and saw that a third build was automatically triggered.
![image](https://user-images.githubusercontent.com/58337007/158026243-fb2ec173-5993-4c7f-938f-a9c50b515b6b.png)

I have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.
By default, the artifacts are stored on Jenkins server locally and can be accessed using the cmdlet below:

`ls /var/lib/jenkins/jobs/project9/builds/2/archive/`
![image](https://user-images.githubusercontent.com/58337007/158026792-838c27f6-bfdf-465b-9e11-0be122307c3b.png)


##### Step 3 - Configured Jenkins to copy files to NFS server via SSH

Now I have my artifacts saved locally on Jenkins server, the next step is to copy them to my NFS server to '/mnt/apps' directory.
Jenkins is a highly extendable application and there are 1400+ plugins available. I will install a plugin that is called "Publish Over SSH".

1. Installed "Publish Over SSH" plugin as follows:

Navigated to the Jenkins dashboard and selected "Manage Jenkins" from the menu.
![image](https://user-images.githubusercontent.com/58337007/154819002-23039742-0602-4d5b-aee8-98278da02680.png)

Selected "Manage Plugins" from the menu.
![image](https://user-images.githubusercontent.com/58337007/154819087-1f280b0d-8809-476b-9f09-a9f78b1a78b6.png)

From the Plugin Manager page, I searched for 'Publish Over SSH' on the Available Tab and installed the plugin as follows:
![image](https://user-images.githubusercontent.com/58337007/158029552-90a9e27e-7f83-4670-8912-b5d97512ed8e.png)
![image](https://user-images.githubusercontent.com/58337007/158029576-fa0bf1ae-706e-41d4-b4db-111c50e70e22.png)
![image](https://user-images.githubusercontent.com/58337007/158029607-fb51b7ed-7eaa-49db-a64d-039a31e0b1db.png)

2. ### Configured the job/project to copy artifacts over to NFS server as follows:

On main dashboard selected "Manage Jenkins" and chose "Configure System" menu item
![image](https://user-images.githubusercontent.com/58337007/158029852-f1aab24b-5d1d-453f-afd5-30f99d76807d.png)

On multiple occasions tried to connect Jenkins Server to the NFS Server but always got errors related to the plugin and key format:
![image](https://user-images.githubusercontent.com/58337007/158030711-dc338f38-e6ea-41b8-8d54-003391e7c090.png)
![image](https://user-images.githubusercontent.com/58337007/158031427-a8839c44-0bc5-4d6e-9c18-39e0ab0509b3.png)


Skipping this portion of the project until the plugin gets to be updated.





