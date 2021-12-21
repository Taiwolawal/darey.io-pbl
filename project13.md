# Ansible Dynamic Assignments (Include) And Community Roles.
We will be continuing from our last project (project 12).
In our Github repository start a new branch and call it dynamic-assignments.
```
git checkout -b dynamic-assignments
```
Create a new folder, name it dynamic-assignments, inside the folder create  a file and name it env-vars.yml.
Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.   

Our layout should now look like this   
![1 0](https://user-images.githubusercontent.com/50557587/146918690-ca4b40bf-36c1-4755-b83a-b2bd54c70c7a.PNG)



Paste the instruction below into the env-vars.yml file.  
![1 1](https://user-images.githubusercontent.com/50557587/146918694-c6e13994-a061-42c0-a6ff-2354872eed7b.PNG)

Notice 3 things to note here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. 

From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:

* [include_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html#include-role-module)
* [include_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html#include-tasks-module)
* [include_vars](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html#include-vars-module)

In the same version, variants of import were also introduces, such as:

* [import_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_role_module.html#import-role-module)
* [import_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_tasks_module.html#import-tasks-module)

We made use of special variables `{{ playbook_dir }}` and `{{ inventory_file }}`. `{{ playbook_dir }}` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. 

`{{ inventory_file }}` on the other hand will dynamically resolve to the name of the inventory file being used, then append `.yml` so that it picks up the required file within the `env-vars` folder.

We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.


Update site.yml file to use of the dynamic assignment (At this point, we cannot test it yet).
![a](https://user-images.githubusercontent.com/50557587/146920640-c367f068-09fe-48ae-98ea-90d2e5647362.PNG)

Now it is time to create a role for MySQL database - it should install the MySQL package, create a database and configure users.

But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there.

These roles are actually production ready, and dynamic to accomodate most of Linux flavours.

With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

## Download Mysql Ansible Role.

You can browse available community roles [here](https://galaxy.ansible.com/home)

We will be using a [MySQL role developed by](https://galaxy.ansible.com/geerlingguy/mysql)  `geerlingguy`.

On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run. 

```
git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
```

Inside `roles` directory create your new MySQL role with `ansible-galaxy install geerlingguy.mysql` and rename the folder to `mysql`

```
mv geerlingguy.mysql/ mysql
```

Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.

`Roles -> MYSQL -> Defaults -> Main.yml`  
![1 2](https://user-images.githubusercontent.com/50557587/146918705-780bfc48-d0eb-499d-b993-cf271433cf33.PNG)

Upload changes into your github.

```
git add .
git commit -m "Commit new role files into GitHub"
git push --set-upstream origin roles-feature
```

Create a pull request amd merge it with the main branch on Github.

## Load Balancer Roles.

We want to be able to choose which Load Balancer to use, `Nginx` or `Apache`, so we need to have two roles respectively:

* Nginx
* Apache

Install the Nginx Ansible Role and rename the folder to nginx

```
ansible-galaxy install geerlingguy.nginx
mv geerlingguy.nginx/ nginx
```

Install the Apache Ansible Role and rename the folder to apache

```
ansible-galaxy install geerlingguy.apache 
mv geerlingguy.apache/ apache
```

![1 3](https://user-images.githubusercontent.com/50557587/146918719-5e6ed125-915f-40cd-a334-337c982f0b10.PNG)

* Since we cannot use both Nginx and Apache load balancer, we need to add a condition to enable either one - this is where we can make use of variables.

* Declare a variable in `defaults/main.yml` file inside the Nginx and Apache roles. Name each variables `enable_nginx_lb` and `enable_apache_lb` respectively.

* Set both values to false like this `enable_nginx_lb: false` and `enable_apache_lb: false`.

* Declare another variable in both roles `load_balancer_is_required` and set its value to `false` as well

![b](https://user-images.githubusercontent.com/50557587/146934014-b89269bc-bb1b-451f-b003-943470a138db.PNG)
![c](https://user-images.githubusercontent.com/50557587/146934028-51ac000e-5c9a-4e26-8004-5f24730568a1.PNG)


* Update both assignment and `site.yml` files respectively

`loadbalancers.yml` file is going to look like this (create this file in your `static-assignments` folder)

```
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```

Add this to the `site.yml` file

```
 - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 
```

Now we can make use of `env-vars\uat.yml` file to define which loadbalancer to use in UAT the environment by setting the respective environmental variable to `true`.

We will activate load balancer, and enable `nginx` by setting these in the respective environment’s `env-vars` file.

```
enable_nginx_lb: true
load_balancer_is_required: true
```

The same must work with `apache` LB, so we can switch it by setting respective environmental variable to `true` and other to `false`.

To test this, we can update inventory for each environment and run Ansible against each environment.


















































