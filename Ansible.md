# Ansible
The content in this file is all about Ansible.

# What is Ansible?
-> Ansible is a open source automation tool
-> By using this tool, we can configuration management, application deployment and task automation
-> By using ansible we can manage infrastructure and deploy software applications and autoamte tasks across multiple systems

# Why Ansible?
-> When a developer writes code on their personal laptop and ensures the application works fine, the challenge is replicating that same working environment across other stages like testing, staging, and production—including the customer's setup. This is where configuration management tools like Ansible come into play.
   Instead of manually configuring each system (installing dependencies, setting up environments, and applying configurations), Ansible automates this process. It ensures that all systems are configured consistently and correctly, no matter where they are.

# How to install Ansible:

step 1: sudo apt update
step 2: sudo apt install ansible
step 3: ansible --version (if we succesfully installed we see status: success in terminal)

# Prerequisite for Ansible. Now we do password less authentication from one ec2 instance (ansible server) to another ec2 instance (target server):

step 1: create two ec2 instances in any cloud providers like (AWS, Azure, GCP) in my case i use AWS. first ec2 instance name is ansible-server and second ec2 instance name is target-server
step 2: login to the two ec2 instances, you can login as your choice, i login to the instances by AWS CLI(command line interface).
step 3: After succesfully login to the two ec2 instances, create a folder with name ansible and after that cd to the folder.
step 4: ssh-keygen, after running this it asks some permissions write yes for it.
step 5: After running this it shows - /home/ubuntu/.ssh/id_rsa
step 6: ls /home/ubuntu/.ssh/ - after running this it shows - authorized keys, id_rsa, id_rsa.pub, known_hosts
step 7: cat /home/ubuntu/.ssh/id_rsa.pub - after running this it shows public key, copy the public key
step 8: Now go to target server and do ssh-keygen 
step 9: ls /home/ubuntu/.ssh/
step 10: vim /home.ubuntu/.ssh/authorized_keys - after running this command it opens the file now paste the public key in this file
step 11: now go to ansible-server and run ssh [ target-server private ip address ] - eg: ssh 172.68.37.100 - after running this the ansible-server ip address changes to target-server ip address

After running all this commands we can succesfully do a password less authentication from ansible-server to target-server


# Now create a filr from ansible-server to target-server: 

i.e., we are creating a file in ansible-server but when we go to target-server and run ls we can see the file that we create on ansible-server.

step 1: now in ansible-server run logout - it shows ansible ip address now, (initially it was target-server private ip address, beacuse we do password less authentication from one server to another server)
step 2: touch inventory - now create a file called inventory
step 3: vim inventory - it opens the file and paste the target-server private ip address in this file and save the file and close the file
step 4: cat inventory - we can see output as - 172.31.16.100 | changed | rc = 05 (if this output appears on your terminal then your succesfully done all the steps till here)
step 5: ansible -i inventory all -m "shell" -a "touch devops_file"

       this is a ansible adhoc command
       ansible - This is the Ansible CLI command used to execute tasks on remote systems.
            -i - is a inventory file
           all - Specifies the target group or all hosts listed in the inventory file.
                 In this case, the command will run on all the servers listed in the inventory
    -m "shell" - -m => Indicates the module to be used.
                 "shell" => The shell module runs shell commands on the remote servers. It is used when you want to execute commands as if typing them into the shell.
-a "touch devops_file" - The -a flag provides arguments to the module.
                         Here, the argument "touch devops_file" is the shell command that will be executed on the target hosts. The touch command creates an empty file named devops_file on each target server.
                         

step 6: now go to target server and run ls - now it shows devops_file in target server


# playbook:

-> playbook in ansible means file name, like if we craete a file in shell then we say shell script file, if python we say python file, similarly in ansible we say ansible playbook
-> In ansible to perform tasks, we use either ansible ad-hoc commands or ansible playbooks
-> for smaller tasks use ansible ad-hoc commands and for larger use ansible playbooks
-> for doing multiple tasks always use ansible playbooks
-> playbooks are nothing but yaml scripts
-> suppose lets a example we have a task to install nginx and start nginx
-> task1: install nginx task2: start nginx

step 1: vim first-playbook.yml -> create a file
       yml - yaml - beacuse ansible playbook supports yaml files

step 2: after opening the file write as below
       ---     [ indicates we are writing a yaml file ] 
       - name: Install nginx
         hosts: all
         become: true

         tasks:
          - name: Install nginx
            apt: 
             name: nginx
             state: present
          - name: start nginx
            service:
             name: nginx
             state: started

step 3: ansible-playbook -i inventory first-playbook.yml -> [to run ansible playbook ]

step 4: sudo systemctl status nginx - after above steps go to target-server and run this command it checks whether nginx is installed or not in target-server

-> now we are succesfully write a ansible playbook.
-> in above we have written one anible playbook
-> suppose if we want to write multiple playbooks then, see below

-> Multiple playbooks
-> task1: install nginx task2: start nginx
   task3: install apache task4: start apache

step 1: vim first-playbook.yml -> create a file
       yml - yaml - beacuse ansible playbook supports yaml files

step 2: after opening the file write as below
       ---
# first ansible-playbook 
       - name: Install nginx
         hosts: all
         become: true

         tasks:
          - name: Install nginx
            apt: 
             name: nginx
             state: present
          - name: start nginx
            service:
             name: nginx
             state: started

# second ansible-playbook
     - name: Install and start Apache
       hosts: all
       become: yes

       tasks:
        - name: Install Apache
          yum:
           name: httpd
           state: present

       - name: Start Apache
         service:
          name: httpd
          state: started
 
-> In above we have written multiple playbooks in single file.
-> This is how we can write playbooks in ansible


# Ansible roles:

-> If we want to write complicated ansible playbooks then we can write by using ansible roles. In ansible roles we use ansible galaxy
-> Ansible Roles are a way to structure your playbooks and related files to make them modular, reusable, and easier to manage.
-> Roles allow you to separate configuration, variables, templates, and tasks into a structured directory format.

# Why ansible roles?

-> Simplify complex playbooks by breaking them into smaller, logical components
-> Promote reusability and modularity.
-> Improve maintainability by organizing tasks and variables logically.

# What is Ansible Galaxy?

-> Ansible Galaxy is a platform and command-line tool provided by Ansible for sharing, discovering, and reusing roles.
-> It provides a public repository of pre-built roles created by the Ansible community, which you can download and use in your projects.

# How to write ansible roles

-> suppose we want to install kubernetes by ansible roles, then

step1: ansible-galaxy role init kubernetes - in output it shows role kuberenetes was created succesfully

-> we can push this in github repo

step1: cd /path/to/your/project
       git init
step2: git add .
       git commit -m "Initial commit of Ansible role"
step3: git remote add origin https://github.com/username/ansible-roles.git
step4: git branch -M main
       git push -u origin main
step5: verify files on github



