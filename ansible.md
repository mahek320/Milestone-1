
## *Ansible*

**Ansible lets you automate virtually any task. Ansible is an open-source automation tool developed by Redhat that is meant for simplifying the configuration management, application deployment, and automation of tasks.**

**Ansible uses simple, human-readable scripts called playbooks to automate your tasks.**

**In Ansible, an inventory is a file with specifying the information of hosts that going to be managed. It contains information such as hostnames, IP addresses, and groups of organization.**

**Ansible is built on top of Python.**

**Handlers in Ansible are tasks that are triggered by other tasks, usually used to respond for the changes that require start , stop , reload or restart the service actions. They are defined in the playbook and executed as per need.**

#### *In this GitHub repository, we have performed four labs:*
1. **LAB 01** - Configured a web server.
2. **LAB 02** - Created a group and a user using a script in Ansible.
3. **LAB 03** - Implemented a handler.
4. **LAB 04** - Created an instance on AWS using Ansible.


## LAB 01

*Build 3 instances t2.micro on amazon linux, controller, host-manager-1 and host-manager-2*
- hostnamectl set-hostname controller
- bash
- ssh-keygen
- ip a s
- cd .ssh
- vim authorized_keys
- cat id_rsa.pub
  
*To establish the connection between the three instances, you need to exchange their keys. For the controller instance, copy the public keys of the other two instances and paste them into the appropriate file using vim. Repeat this process for the other two instances to complete the connection setup.*

*Controller Terminal*
- systemctl start sshd 
- systemctl enable sshd

*To check if the connection is established between the instances, allow the ICMP port in the security rule and then use the ping command.*
- ip a s
- ping (ip-address)

*Controller - Install ansible*
- yum install ansible* -y
- ansible --version
- cd /etc/ansible/
- vim ansible.cfg

**Paste the below content inside the vim file**

```cfg

# config file for ansible -- http://ansible.com/
# ==============================================

# nearly all parameters can be overridden in ansible-playbook 
# or with command line flags. ansible will read ANSIBLE_CONFIG,
# ansible.cfg in the current working directory, .ansible.cfg in
# the home directory or /etc/ansible/ansible.cfg, whichever it
# finds first

[defaults]

# some basic default values...

hostfile       = /etc/ansible/hosts
library        = /usr/share/ansible
remote_tmp     = $HOME/.ansible/tmp
pattern        = *
forks          = 5
poll_interval  = 15
sudo_user      = root
#ask_sudo_pass = True
#ask_pass      = True
transport      = smart
remote_port    = 22

# additional paths to search for roles in, colon seperated
#roles_path    = /etc/ansible/roles

# uncomment this to disable SSH key host checking
#host_key_checking = False

# change this for alternative sudo implementations
sudo_exe = sudo

# what flags to pass to sudo
#sudo_flags = -H

# SSH timeout
timeout = 10

# default user to use for playbooks if user is not specified
# (/usr/bin/ansible will use current user as default)
#remote_user = root

# logging is off by default unless this path is defined
# if so defined, consider logrotate
#log_path = /var/log/ansible.log

# default module name for /usr/bin/ansible
#module_name = command

# use this shell for commands executed under sudo
# you may need to change this to bin/bash in rare instances
# if sudo is constrained
#executable = /bin/sh

# if inventory variables overlap, does the higher precedence one win
# or are hash values merged together?  The default is 'replace' but
# this can also be set to 'merge'.
#hash_behaviour = replace

# How to handle variable replacement - as of 1.2, Jinja2 variable syntax is
# preferred, but we still support the old $variable replacement too.
# Turn off ${old_style} variables here if you like.
#legacy_playbook_variables = yes

# list any Jinja2 extensions to enable here:
#jinja2_extensions = jinja2.ext.do,jinja2.ext.i18n

# if set, always use this private key file for authentication, same as 
# if passing --private-key to ansible or ansible-playbook
#private_key_file = /path/to/file

# format of string {{ ansible_managed }} available within Jinja2 
# templates indicates to users editing templates files will be replaced.
# replacing {file}, {host} and {uid} and strftime codes with proper values.
ansible_managed = Ansible managed: {file} modified on %Y-%m-%d %H:%M:%S by {uid} on {host}

# by default, ansible-playbook will display "Skipping [host]" if it determines a task
# should not be run on a host.  Set this to "False" if you don't want to see these "Skipping" 
# messages. NOTE: the task header will still be shown regardless of whether or not the 
# task is skipped.
#display_skipped_hosts = True

# by default (as of 1.3), Ansible will raise errors when attempting to dereference 
# Jinja2 variables that are not set in templates or action lines. Uncomment this line
# to revert the behavior to pre-1.3.
#error_on_undefined_vars = False

# set plugin path directories here, seperate with colons
action_plugins     = /usr/share/ansible_plugins/action_plugins
callback_plugins   = /usr/share/ansible_plugins/callback_plugins
connection_plugins = /usr/share/ansible_plugins/connection_plugins
lookup_plugins     = /usr/share/ansible_plugins/lookup_plugins
vars_plugins       = /usr/share/ansible_plugins/vars_plugins
filter_plugins     = /usr/share/ansible_plugins/filter_plugins

# don't like cows?  that's unfortunate.
# set to 1 if you don't want cowsay support or export ANSIBLE_NOCOWS=1 
#nocows = 1

# don't like colors either?
# set to 1 if you don't want colors, or export ANSIBLE_NOCOLOR=1
#nocolor = 1

# the CA certificate path used for validating SSL certs. This path 
# should exist on the controlling node, not the target nodes
# common locations:
# RHEL/CentOS: /etc/pki/tls/certs/ca-bundle.crt
# Fedora     : /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem
# Ubuntu     : /usr/share/ca-certificates/cacert.org/cacert.org.crt
#ca_file_path = 

# the http user-agent string to use when fetching urls. Some web server
# operators block the default urllib user agent as it is frequently used
# by malicious attacks/scripts, so we set it to something unique to 
# avoid issues.
#http_user_agent = ansible-agent

[paramiko_connection]

# uncomment this line to cause the paramiko connection plugin to not record new host
# keys encountered.  Increases performance on new host additions.  Setting works independently of the
# host key checking setting above.
#record_host_keys=False

# by default, Ansible requests a pseudo-terminal for commands executed under sudo. Uncomment this
# line to disable this behaviour.
#pty=False

[ssh_connection]

# ssh arguments to use
# Leaving off ControlPersist will result in poor performance, so use 
# paramiko on older platforms rather than removing it
#ssh_args = -o ControlMaster=auto -o ControlPersist=60s

# The path to use for the ControlPath sockets. This defaults to
# "%(directory)s/ansible-ssh-%%h-%%p-%%r", however on some systems with
# very long hostnames or very long path names (caused by long user names or 
# deeply nested home directories) this can exceed the character limit on
# file socket names (108 characters for most platforms). In that case, you 
# may wish to shorten the string below.
# 
# Example: 
# control_path = %(directory)s/%%h-%%r
#control_path = %(directory)s/ansible-ssh-%%h-%%p-%%r

# Enabling pipelining reduces the number of SSH operations required to 
# execute a module on the remote server. This can result in a significant 
# performance improvement when enabled, however when using "sudo:" you must 
# first disable 'requiretty' in /etc/sudoers
#
# By default, this option is disabled to preserve compatibility with
# sudoers configurations that have requiretty (the default on many distros).
# 
#pipelining = False

# if True, make ansible use scp if the connection type is ssh 
# (default is sftp)
#scp_if_ssh = True

[accelerate]
accelerate_port = 5099
accelerate_timeout = 30
accelerate_connect_timeout = 5.0

```

- ansible --version

  #### *Create vim host under etc/ansible*
- vim hosts
  *paste ip of the two manager nodes in the below format*
  
   [us-server]
  
   172.31.1.23
  
- ansible all --list-hosts
- ansible all -m ping
 
  *type yes 2 times*
  
**Note** : *When the output is green, it indicates that the command was executed successfully. When it is gold, it means Ansible made changes on the remote machine. When it is red, it indicates an error.*

*Inorder to configure web server*
 - cd /etc/ansible
 - vim configure.yml

**Paste the below content inside vim file**

```yaml

   ---

- name: configure apache server
  hosts: all

  tasks:

     - name: installed httpd pkg
       dnf:
            name: httpd
            state: latest
     - name: copy index.html file
       copy:
            src: index.html
            dest: /var/www/html/index.html
     - name: started apache
       systemd:
             name: httpd
             state: started
             enabled: true

```

       
 - ansible-playbook configure.yml --syntax-check
 - cat >index.html
 - ansible-playbook configure.yml
   
  *Paste the public ip of the host on the browser*

## LAB 02

 ### *To make a group and a user using script in ansible.*

    vim playbook.yml

**Paste the below content inside the vim file**

```yaml
---

- name: creating some user & group
  hosts: all


  tasks:

    - name: create a group
      group:
           name: devops
           state: present
    - name: create an user thor
      user:
           name: thor
           uid: 1200
           shell: /bin/bash
           home: /home/india
           groups: devops
           state: present
    - name: install smb pkg
      yum:
           name: cifs-utils
           state: present
    - name: install ftp
      yum:
           name: nfs-utils
           state: present
```

- ansible-playbook playbook.yml
 
#### *Go to manager node and run the below command to check the user created inside group*
- cat /etc/group

## LAB 03

### *Handler*
- vim configure-appache.yml   (*Inside ansible directory cd /etc/ansible*)

(*Paste the below content*)

```yaml
  ---
- name: configure apache with handler
  hosts: all
  tasks:
    - name: installed httpd
      dnf:
        name: httpd
        state: latest
 
    - name: copied httpd.conf file on target machine
      copy:
        src: httpd.conf
        dest: /etc/httpd/conf/httpd.conf
 
    - name: copied index.html
      copy:
        src: index.html
        dest: /var/www/html/index.html
 
    - name: restart the httpd service
      systemd:
        name: httpd
        state: restarted
        enabled: true
      notify: restart_httpd
  
  handlers:
    - name: restart_httpd
      service:
        name: httpd
        state: restarted
        enabled: true
 
    - name: restart_firewalld
      service:
        name: firewalld
        state: restarted
        enabled: true
```

(*Now make an index file*)
  - cat > index.html
  - yum install httpd -y
  - cd /etc/ansible
  - vim /etc/httpd/conf/httpd.conf     (#*Add a comment at start #Mahek Shetty and then save it*)
  - ll              (#*Check for index.html file*)
  - ansible-playbook configure-appache.yml --syntax-check

**Note** : *Handler and task should have same indentation*

  - ansible-playbook configure-appache.yml

*Now go to manager node and check if the files are reflecting*

(*To check if httpd is installed*)
 -  rpmquery httpd
    
(*To check if the index html file is available*)
 -  cd /var/www/html
 -  ll
   
(*To check conf file*)
 -  cd /etc/httpd/conf
 -  ll
   
(*Check if the changes made in the /etc/httpd/conf/httpd.conf is reflecting*)
 - cat httpd.conf
 - ip a s
 - curl http://172.31.34.36


## LAB 04 

### *Configure AWS with ansible*
-  cd /etc/ansible
-  vim creds.yml        (*Paste the aws_access_key: and aws_secret_key:*)
-  vim ec2.yml

**Paste the below content in the file and change the ami, region, security group and key name**

```yaml
  ---
- name: create an ec2 instance
  hosts: all
  vars_files:
        - creds.yml
  tasks:
    - name: install pip
      yum:
        name: pip
        state: present
    - name: install boto3
      pip:
        name: boto3
        state: present

    - name: create an ec2 instance using ansible
      amazon.aws.ec2_instance:
        aws_access_key: "{{ aws_access_key }}"
        aws_secret_key: "{{ aws_secret_key }}"
        key_name: "ans-key"
        instance_type: t2.micro
        security_group: sg-0891eca823714b7e5
        region: ap-southeast-2
        count: 1
        image_id: ami-0e8fd5cc56e4d158c
        tags:

```

 ansible-playbook ec2.yml



**Note** : Due to multiple control z and control d many swap files(duplicate files are created) and thus needs to be removed

- >> go to terminal >> etc/ansible >> ls -a (shows swap files) >> rm -rf (hostfiles)




   
