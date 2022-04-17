#### Introducing Dynamic Assignment Into Our Structure

In my https://github.com/Jagembe/ansible-config-mgt GitHub repository start a new branch and call it dynamic-assignments.

Created a new folder, named it dynamic-assignments. Then inside this folder, created a new file and named it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.
![image](https://user-images.githubusercontent.com/58337007/161097259-4842f3d4-ca38-4106-b493-ddc181ae397e.png)

My GitHub shall have following structure by now.
![image](https://user-images.githubusercontent.com/58337007/161097808-bb18f7d0-ea36-4d95-b9a7-f64308836d76.png)

Note: Depending on what method is used in the previous project you may have or not have roles folder in your GitHub repository – if you used ansible-galaxy, then roles directory was only created on your Jenkins-Ansible server locally. It is recommended to have all the codes managed and tracked in GitHub, so you might want to recreate this structure manually in this case – it is up to you.

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

My layout now looks like this
![image](https://user-images.githubusercontent.com/58337007/161146967-2aac209c-aed8-4f00-bd5c-80faafaf8916.png)

![image](https://user-images.githubusercontent.com/58337007/161147485-d0eccd6f-8183-4d94-9619-3913fd08e566.png)

Pasted the instruction below into the env-vars.yml file.
```
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always
```

![image](https://user-images.githubusercontent.com/58337007/161160428-50c49b7f-3617-4b9f-973b-4da7577555a9.png)

## Update site.yml with dynamic assignments
Update site.yml with dynamic assignments

Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

site.yml should now look like this.

![image](https://user-images.githubusercontent.com/58337007/161161217-d06f4634-b5dd-4a53-a1fe-edcc5a5e8602.png)
