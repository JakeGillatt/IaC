# What is Infrastructure as Code (IaC) ?

Infrastructure as code (IaC) is a way to manage infrastructure resources, like servers and databases, using code and automation tools.

#
# What are the benefits of IaC?

- Consistency: It ensures the infrastructure is always in a known and reproducible state to eliminate configuration drift and human errors.

- Agility: It enables teams to quickly provision and scale resources up or down depending on requirements or business needs.

- Scalability: It supports the management of large and complex infrastructures. The code can be modularized and reused, and then shared across teams.

- Auditability: It provides a transparent and auditable record of the changes made to the infrastructure, which helps with compliance and security.

- Collaberation: It encourages collaboration and knowledge sharing between team members, as the code can be version-controlled, reviewed and tested.

#
# Who is using IaC and how is it used in the industry?


IaC is used by a wide range of organizations, from startups to large enterprises, across various industries, such as finance, healthcare, retail, and media. Some of the notable companies that use IaC include Netflix, Airbnb, Capital One, and GE.

- Provisioning: It can be used to provision and configure infrastructure resources, such as virtual machines, storage, load balancers, and databases, in an automated and repeatable way.

- Scaling: It can be used to scale up or down resources, based on demand or usage patterns, by adjusting the code that defines the desired state of the infrastructure.

- Testing: It can be used to test the infrastructure code, by spinning up a test environment, running automated tests, and tearing down the environment when the tests are complete.

- Disaster Recovery: It can be used to recover from a disaster, by creating a new environment from scratch, based on the code that defines the desired state of the infrastructure.

- Compliance: It can be used to enforce compliance policies, such as security and governance, by incorporating them into the code that defines the desired state of the infrastructure. 

#
# What is configuration management?

Configuration management is the process of managing software and hardware systems in a consistent, reliable, and auditable way. It involves keeping track of system components, controlling changes, automating deployments, and monitoring for compliance. Tools like Ansible, Puppet, Chef, and SaltStack are used to support configuration management.

#
# What is Ansible?

Ansible is an open-source automation tool for managing software and infrastructure configurations, deployments, and orchestration. It uses simple, human-readable language to define automation tasks and playbooks, which can be executed on multiple systems. Ansible is known for its simplicity, ease of use, and scalability, and it can be used to manage a wide range of systems, including servers, network devices, cloud resources, and containers.

#
# What are the benefits of Ansible?

- Automation: It allows users to automate repetitive and complex tasks, such as software provisioning, configuration management, and application deployment, which can save time and reduce the risk of errors and inconsistencies.

- Scalability: It can manage thousands of systems simultaneously, making it a scalable solution for large-scale environments.

- Efficiency: It agentless architecture and modular design make it efficient and easy to use, reducing the overhead and simplifying the deployment process.

- Flexibility: It can manage a wide range of systems, including servers, network devices, cloud resources, and containers, and it provides a large collection of pre-built modules and playbooks that can be easily customized or extended to fit specific use cases.

- Consistency: It ensures that IT infrastructure configurations and deployments are consistent across different systems, reducing the risk of errors and improving the reliability of the IT environment.

- Collaboration: It provides a simple and transparent way for teams to collaborate on IT automation tasks, allowing them to share knowledge and best practices and work together more effectively.

#
# What are the use cases?

Ansible is a powerful automation tool that can be used for a wide range of use cases related to IT infrastructure and software deployment. Some of the most common use cases for Ansible are:

- Configuration Management: It can be used to manage and automate the configuration of servers, network devices, and other IT infrastructure components, ensuring consistency and reducing the risk of errors and misconfigurations.

- Provisioning: It can be used to automate the process of provisioning new servers and other IT resources, reducing the time and effort required for manual setup and configuration.

- Application Deployment: It can be used to automate the deployment of applications, databases, and other software components across multiple servers and environments, reducing the risk of errors and ensuring consistency.

- Continuous Delivery: It can be used to automate the entire software delivery pipeline, from code deployment to testing and production rollout, enabling faster and more frequent releases with less risk.

- Orchestration: It can be used to orchestrate complex IT workflows and processes, such as disaster recovery, database backups, and system updates, ensuring reliability and reducing the risk of downtime.

- Cloud Management: It can be used to automate the management of cloud infrastructure and resources, such as virtual machines, containers, and storage, across multiple cloud providers and regions.

#
# What is YAML and what are Playbooks?

- YAML is a simple format used to define data structures, often used in Ansible to configure IT infrastructure. 

- Ansible playbooks are YAML files that define tasks to be executed on target hosts, allowing for automation of a wide range of tasks including system configuration and application deployment.

#
# Ansible diagram:
![MicrosoftTeams-image](https://github.com/JakeGillatt/IaC/assets/129315605/7d25600c-c8a9-4df7-add7-61aee4bf9859)

#
# Setting up Ansible on our Local Virtual Machine

1. Launch a virtual machine using ubuntu 18.04, with vagrant. - This will be our Ansible Controller VM
2. SSH into the VM and run the following commands:
```
sudo apt update -y
sudo apt upgrade -y

sudo apt install software-properties-common
sudo apt-add-repository ppa:ansible/ansible

sudo apt update -y
sudo apt install ansible

ssh vagrant@192.168.33.10 # password is vagrant
```
3. Enter the password when prompted - When you type the password the characters will be invisible
4. Now we can launch our APP VM and our DB VM, run the update and upgrade commands on each.

#
# Using an Ansible Controller VM to connect to other VM's and copy files over

- We can connect into other VM's from the Controller using: e.g `ssh vagrant@192.168.33.10` – connects to web app – password is vagrant

1. Connect to our Controller using `vagrant ssh controller`
2. Go onto the /etc/ansible/ directory with `cd /etc/ansible/`
3. Edit the 'hosts' file with `sudo nano hosts`
4. Here we need to add our Web VM and DB vm by adding the following:
```
[web]
192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant

[db]
192.168.33.11 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
```
5. Run `Sudo ansible all -m ping` to test the connections to both VM's
6. If the db fails to ping, use `sudo nano /etc/ansible/ansible.conf` and add `Add host_key_checking = false` to the file, then save and repeat the ping command.
7. Once both VM's are connected we can use the controller to interact with the VM's and retrieve information from them with commands such as:
```
Sudo ansible web -a “date” # gets the current time/date from the web vm
sudo ansible db -a "date" # ^ gets date/time from db vm

sudo ansible all -a "free" # check available memory within the vm’s

sudo ansible all -a "ls" # gets info on files/folders within the vm’s
```
- These are just some examples ^
8. To copy a file from the Controller VM to the web VM we can use `sudo ansible web -m copy -a "src=/etc/ansible/'file name' dest=/home/vagrant"` - replace the 'file name' with the file you wish to copy.
- In this case I sent a 'testing.txt' file from my Controller to my Web VM's home directory.

#
# Using a playbook to install nginx

1. In the Controller VM, cd to `/etc/ansible/`
2. Run `sudo nano install-nginx-playbook.yml` to create a YAML file and enter the following:
```
# creating a playbook to install nginx

# YAML file starts with ---

---
# where would you like to install nginx?

- hosts: web
  

# would you like to see the logs?
  
  gather_facts: yes

# do we need admin access? - "become: true" adds sudo to each command we use

  become: true

# add the instructions - commands. "pjg" = package, "state=present" tells us the statusA        

  tasks:
  - name: Install nginx in web-server

    apt: pkg=nginx state=present
```
3. Save the file and use `sudo ansible-playbook install-nginx-playbook.yml` to launch the playbook
- nginx will now be installed onto the web VM. Enter the web vm ip into your browser to test nginx is working

#
# Using a playbook to install NodeJs

1. In the Controller VM, cd to `/etc/ansible/`
2. Run `sudo nano install-nodejs-playbook.yml` to create a YAML file and enter the following:
```
# creating a playbook to install nodejs

# YAML file starts with ---

---
# where would you like to install nodejs?

- hosts: web
  

# would you like to see the logs?

  gather_facts: yes

# do we need admin access? - "become: true" adds sudo to each command we use

  become: true

# add the instructions - commands. "pkg" = package, "state=present" tells us the statusA        
  tasks:
  - name: Install python
    apt: pkg=python state=present

  tasks:
  - name: Install nodejs
    apt: pkg=nodejs state=present

  tasks:
  - name: Install npm

    apt: pkg=npm state=present
```
3. Save the file
4. Transfer the app files to the web vm with `scp -r C:/Users/J4k3_/Documents/"SPARTA GLOBAL"/Virtualisation/app vagrant@192.168.33.10:/home/vagrant`
5. Use `sudo ansible-playbook install-nodejs-playbook.yml` to launch the playbook
- nodejs will now be installed along with its dependancies

#
# Using a playbook to install MongoDB on the database vm

1. In the Controller VM, cd to `/etc/ansible/`
2. Run `sudo nano install-mongodb-playbook.yml` to create a YAML file and enter the following:
```
# installing mongodb in the db VM/node
---

# where would you like to install nodejs?

- hosts: db

# would you like to see the logs?

  gather_facts: yes

# do we need admin access? (adds sudo to our commands)

  become: true

# add the instructions / commands to install:

  tasks:
  - name: install mongodb on db VM/node
    apt: pkg=mongodb state=present

# check status with adhoc commands 
```
3. Save the file and use `sudo ansible-playbook install-mongodb-playbook.yml` to launch the playbook

#
# Using a playbook to automate changing the "bind_ip" in the mongodb.conf file

1. create a playbook with `sudo nano mongodb-config.yml and enter:
```
---
# where would you like to install nodejs?      

- hosts: db

# would you like to see the logs?

  gather_facts: yes

# do we need admin access? - "become: true" ad$

  become: true

# add the instructions - commands. "pkg" = pac$  tasks:
  - name: change bind ip for mongodb
    lineinfile:
      path: /etc/mongodb.conf
      regexp: 'bind_ip = 0.0.0.10'
      line: 'bind_ip = 0.0.0.0'
      backrefs: yes
  - name: restart mongo
    shell: systemctl restart mongodb

  - name: enable mongodb
    shell: systemctl enable mongodb
```
2. run the playbook and this will change the bind ip for mongodb

#
# Use a playbook to add an environment variable DB_HOST to connect the web to the db

1. create a playbook with `sudo nano add-dbhost.yml and enter:
```
- hosts: web


# would you like to see the logs?

  gather_facts: yes
  become: true

  - name: change db host
    shell: export DB_HOST=mongodb://192.168.33.11:27017/posts
```
2. run the playbook and this will add the variable DB_HOST
