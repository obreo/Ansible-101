# Ansible-101
This is a basic tutorial of Ansible deploying Apache server on 2 EC2 instances

## Introduction

Ansible is an automating tool used to manage multiple machines together. Usage examples include patching one to hundrades of machines with a single command. Or installing an application and configuring it over several servers. You can also deploy EC2 instances and configure them to the cloud, as used in Terraform.

## How it works

Ansible depends mainly on the host file & modules. A host file is where the user identifies the IP addresses of the target machines. These IP address are categorized under group or set of groups.
### Host file
```
[linux]
127.0.0.1  # ip
..         # ip2
.          # etc..

[linux: vars] # optional

```

Below `linux:vars` you may add parameters such as username and password credintials to login to the instances.

### Modules
A library of programs that can be used as a task for a specific job. Such as bash module to write a script, a module to check the ping, and many other modules that can be found in the [ansible modules](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html).

## Installation

Follow the offical [documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) for installing Ansible. 

## Configuration
All the ansible files are included in the `/etc/ansible` directory.

## Demo
Here are examples of running ansible commands:

```
# Run a bash command using '-a'
 ansible GROUP_NAME -a "yum install httpd -y" --user=USERNAME_of_INSTANCE --key-file KEYPAIR.pem

# Run a module using '-m'
 ansible GROUP_NAME -m ping --user=USERNAME_of_INSTANCE --key-file KEYPAIR.pem -vvv #-vvv is used for logging
 ```
 Note: If you register the username & password parameters in the hosts file, or you register the authorized keys in ssh cofiguration directory then you won't need to write the `--user=USERNAME_of_INSTANCE --key-file KEYPAIR.pem` each time.
 
 ## Ansible-books
 An ansible book is a script file that contains one or multiple tasks in which each task assigned to a host and contains a single or multiple modules. ansible book file is useful to create a shareable configuration file that does a particular job. An Ansible book contains a play or multiple plays, each play has the host group identified and the tasks, where each task has a module to do a job.
 
 Here is an example of `apache.yml`:
 
```
---
- name: shell commands     # Play
  hosts: linux                      # Group specified
  become: true      

  tasks:                                
    - name: Run shell commands          # Task1
      shell: |                          # Modlule 1: shell. The | symbol used to write multiple cammands 
        sudo yum install httpd -y
        echo "This page was configured by Ansible" >> /var/www/html/index.html
        service httpd restart
        echo "Done"
```

To run the script:

```
ansible-playbook apache.yml
```

Here is another example to check if Nano is installed on the servers, otherwise it will be installed:

```
---
- name: nano-checker     #Play
  hosts: linux                      #group specified
  become: true      

  tasks:                                
    - name: Check if nano installed          #task1
      yum:                          # modlule 1
        name: nano                  #package name
        state: latest               #check if latest version available. You can used "present" as well.
```
You can use `absent` at the state node, which will delete the package if found installed.
