# Ansible Inventory Documentation: `/etc/ansible/hosts`

This document outlines different command-line configurations that can be used in the `/etc/ansible/hosts` file. These are personal notes for reference.

## Examples of Commands

## INI Format (Personal Notes)
Below are examples of INI-style Ansible inventory configurations for personal documentation:

  ### Basic Host Configuration
  Defines the host web with its hostname or IP and specifies SSH as the connection method.
  
  ```ini
  #web ansible_host=server1.company.com ansible_connection=ssh
  ```
  ### Specifying a Port
  Specifies that SSH should connect to port 22 for the host web.
  
  ``` ini
  #web ansible_host=server1.company.com ansible_connection=ssh ansible_port=22
  ```
  
  ### Using a Specific User
  Indicates that the SSH connection will use the root user for the host web.
  ``` ini
  #web ansible_host=server1.company.com ansible_connection=ssh ansible_user=root
  ```
  
  ### Providing a Password
  
  Defines the SSH password for the web host. Avoid using plaintext passwords where possible; consider alternatives like SSH keys or vault encryption.
  
  ``` ini
  #web ansible_host=server1.company.com ansible_connection=ssh ansible_ssh_pass=*Password*
  ```

  ### general example 1

  ```ini
# Sample Inventory File

# Web Servers
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

# Database Servers
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Password123!


[web_servers]
web1
web2
web3

[db_server]
db1

[all_servers:children]
db_servers
web_servers
  ```

  ### general example 2

  ``` ini
# Sample Inventory File

# Web Servers
web_node1 ansible_host=web01.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass
web_node2 ansible_host=web02.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass
web_node3 ansible_host=web03.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass

# DB Servers
sql_db1 ansible_host=sql01.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass
sql_db2 ansible_host=sql02.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass

[db_nodes]
sql_db1
sql_db2

[web_nodes]
web_node1
web_node2
web_node3

[boston_nodes]
sql_db1
web_node1

[dallas_nodes]
sql_db2
web_node2
web_node3



[us_nodes:children]
boston_nodes
dallas_nodes

  ```

  
  ### Localhost Connection
  Specifies that the connection to localhost will use the localhost connection type.
  
  ``` ini
  #localhost ansible_connection=localhost
  ```

## YAML Format

###The YAML format is structured and flexible compared to the INI format. Below is an example of a YAML-based inventory structure:

```yaml
all:
  children:
    webservers:
      hosts:
        web1.example.com:
        web2.example.com:
    dbservers:
      hosts:
        db1.example.com:
        db2.example.com:
```

### general exampl 1

``` yaml
# Grouping and Parent-Child Relationships Example in YAML Format

all:
  children:
    webservers:
      children:
        webservers_us:
          hosts:
            server1_us.com:
              ansible_host: 192.168.8.101
            server2_us.com:
              ansible_host: 192.168.8.102
        webservers_eu:
          hosts:
            server1_eu.com:
              ansible_host: 10.12.0.101
            server2_eu.com:
              ansible_host: 10.12.0.102

```

*This YAML structure illustrates the grouping of servers into regions (webservers_us, webservers_eu) under a parent group (webservers), which is further nested under the all group. Each server is specified with its hostname and IP address using the ansible_host parameter.*


# Ansible Variables
*Stores information that varies with each host*

* all the Variables in the inventroy file such as:
  - ansible_host 
  - ansible_connection 
  - ansible_user 
  - ansible_ssh_pass  // this is for password in linux machine
  - ansible_passworf  // this is for password in windows machine*

* we can also use a Valriable in the ansible yaml playbook we can simply add a var Dirctory in a key-value pair format as below:

``` yaml 
name: 
hosts:
vars:
  dns_server: 10.0.0.0
```

or we can use a sepirate file called variables and add our variables in a key-value pair format 

``` sh
# this is variable file 

key1 : value1
key2: value2
```

* How you can recall a variable in an ansible yaml playbook

``` yaml
---
name: Add DNS server to resolv.conf
 hosts: localhost
 vars:
 dns_server: 10.1.250.10
 tasks:- lineinfile:
 path: /etc/resolv.conf
 line: 'nameserver 10.1.250.10' {{ dns_server }}’ # notice {{variable_name}}

```

### Remember this format we are using is called Jinja2 Templating
``` sh
 source: ‘{{ inter_ip_range }}’
 source: SomeThing{{ inter_ip_range }}SomeThing
  ```

## Variable Types✨

### String Variables

  * String variables in Ansible are 
  sequences of characters. 
  * They can be defined in a playbook, 
  inventory, or passed as command line 
  arguments. 

``` bash
username: "admin"
```

### Number Variables

  * Number variables in Ansible can hold 
  integer or floating-point values. 
  * They can be used in mathematical 
  operations.

  ``` bash
 max_connections: 100
  ```
### Boolean Variables
  * Boolean variables in Ansible can hold 
  either true or false. 
  * *They are often used in conditional 
  statements. 
  ``` bash
 debug_mode: true
```

| Valid Values                                                                 | Description     |
|------------------------------------------------------------------------------|------------------|
| **Truthy Values**                                                            |                  |
| True, 'true', 't', 'yes', 'y', 'on', '1', 1, 1.0                            | Truthy values    |
| **Falsy Values**                                                             |                  |
| False, 'false', 'f', 'no', 'n', 'off', '0', 0, 0.0                        | Falsy values     |

### List Variables

* List variables in Ansible can hold an 
ordered collection of values. 
* The values can be of any type. 

``` bash
packages:
  - nginx
  - git
  - postgresql
```

  - Note:
    * you can access a certine value in the variable list by calling its index as below:

  ```yaml
  {{ packages[0] }}
  ```

  * or you call call them all if you are using a loop 

  ```yaml
 loop: "{{ packages }}"
  ``` 

  ### Dictionary Variables

  * Dictionary variables in Ansible can 
  hold a collection of key-value pairs. 
  * The keys and values can be of any 
  type. 

``` yaml
user:
 name: "admin"
 password: "secret"
```
  - Note:
    * you can access a certine value in the variable Dictionary by calling its key as below:
  
``` yaml
 msg: "Username: {{ user.name }}, Password: {{ user.password}}"
```
## Variable Precedence

Ansible Variable Precedence determines the order in which variables are assigned and executed. This means you can define variables at different levels (e.g., group level, host level, playbook level), and Ansible will resolve conflicts by using the variable defined at the highest precedence level.

- Precedence Order: 

The precedence order (from lowest to highest) is as follows:

* Role defaults (defaults/main.yml)
* Inventory group variables (e.g., [group_name:vars])
* Inventory host variables
* Inventory file or script group variables
* Inventory file or script host variables
* Playbook group variables
* Playbook host variables
* Host facts (gathered facts)
* Play vars
* Task vars (only within the task)
* Role vars (vars/main.yml)
* Block vars (only within the block)
* Task vars (set using set_fact)
* Extra vars (provided on the command line with -e)*

# Ansible Variable Scopes and Magic Variables

Variable scopes in Ansible allow you to associate variables with specific contexts like a host, a group, a playbook, or even globally. This helps manage configurations more effectively.

## Key Variable Scopes

- **Host-Level Variables**: Assigned to a specific host.
- **Group-Level Variables**: Shared among a group of hosts.
- **Playbook Variables**: Specific to the playbook where they are defined.
- **Global Variables**: Defined using the `-e` or `--extra-vars` option, overriding all others.

## Examples of Variable Scopes

#### Host-Level Variable

```ini
# In /etc/ansible/hosts
[web]
server1 ansible_host=192.168.1.101 app_env=production
server2 ansible_host=192.168.1.102
```

#### Group-Level Variable

```ini
# In /etc/ansible/hosts
[web:vars]
app_env=development
```

#### Playbook-Level Variable

``` yaml
# playbook.yml
- name: Set up web servers
  hosts: web
  vars:
    app_env: staging
  tasks:
    - name: Show environment variable
      debug:
        msg: "App environment is {{ app_env }}"
```

## Magic Variables

Magic variables in Ansible are predefined variables that provide access to runtime context. These variables allow you to reuse and reference variables assigned to specific hosts or groups dynamically.

### Common Magic Variables

- **hostvars**: Access variables of other hosts.
- **groups**: Access group-level information.
- **inventory_hostname**: The name of the host in the inventory.
- **play_hosts**: List of all hosts in the current play.
- **ansible_play_batch**: Subset of hosts in the current batch.

### Example of Using Magic Variables

#### Accessing Variables of Another Host

```yaml
# playbook.yml
- name: Example of hostvars
  hosts: all
  tasks:
    - name: Retrieve app_env of server1 and use it on server2
      debug:
        msg: "Server1's app_env is {{ hostvars['server1']['app_env'] }}"
```
#### Referencing Group Variables
```yaml
# playbook.yml
- name: Example of groups magic variable
  hosts: web
  tasks:
    - name: Show all hosts in the 'web' group
      debug:
        msg: "Web hosts are {{ groups['web'] }}"

```

#### Using Inventory Hostname
```yaml
# playbook.yml
- name: Example of inventory_hostname
  hosts: all
  tasks:
    - name: Display the hostname
      debug:
        msg: "This task is running on {{ inventory_hostname }}"

```

#### Reusing Variables Across Hosts
You can assign a variable to one host and reuse it in another host by leveraging the hostvars magic variable.

```yaml
# playbook.yml
- name: Example of reusing variables
  hosts: all
  tasks:
    - name: Set a fact on server1
      set_fact:
        custom_var: "Hello from server1"
      when: inventory_hostname == "server1"

    - name: Reuse server1's variable on other hosts
      debug:
        msg: "Variable from server1 is {{ hostvars['server1']['custom_var'] }}"

```

For more detailed information on Ansible magic variables and variable scopes, please refer to the official Ansible documentation: [Ansible Documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html) .


## Ansible Facts

By defualt when you run ansible on a remote machine or group of remote machines ansible start a module called (Setup) automattically and start gathering facts on each host that this playbook run into

* thats so when you run a single task in a playbook you will see two tasks outputs

``` bash

 TASK [Gathering Facts] **************************************************************

 TASK [task_one] *******************************************************
```

the output of this facts is storing by defualts in a variable called <ansible_facts>

``` yaml
--- name: Print hello message
 hosts: all
 tasks:- debug:
 var: ansible_facts
```

you can stop this gathering by editing the ansible configuration that in the /etc/ansible/ansible.cfg
or your personal configuration at your home dir ~/.ansible.cfg

or the playbook configuration

Disabling Fact Gathering
To disable fact gathering, you can change the following setting in your configuration:

```yaml
[defaults]
gathering = explicit
```
Or, you can disable it directly in your playbook:

``` yaml
- name: Example Playbook
  hosts: all
  gather_facts: no
  tasks:
    - name: Run a task without gathering facts
      debug:
        msg: "This task runs without gathering facts."
```
# Ansible Configuration Language

Ansible uses a configuration language where we tell Ansible what it should do. The primary components of an Ansible playbook include:

## Playbook

A single YAML file that contains one or more plays.

### Play

Defines a set of activities (tasks) to be run on hosts.

### Task

An action to be performed on the host, such as:

- Execute a command
- Run a script
- Install a package
- Shutdown/Restart

## Example Playbook

Here is an example of a simple playbook:

```yaml
- name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date
      
    - name: Execute script on server
      script: test_script.sh
      
    - name: Install httpd service
      yum: 
        name: httpd
        state: present
        
    - name: Start web server
      service:
        name: httpd
        state: started
```
#### Splitting into Multiple Plays
You can also split the playbook into two plays in the same file:
remember that this each playbook will be a list of dictinory 

``` yaml

- name: Play 2
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date
      
    - name: Execute script on server
      script: test_script.sh
      
    - name: Install web service
      yum: 
        name: httpd
        state: present
        
    - name: Start web server
      service:
        name: httpd
        state: started
```
# Ansible Modules

The different actions run by tasks in Ansible are called **modules**. Modules are the building blocks of Ansible tasks and allow you to perform various operations on your managed hosts.

## Getting Information on Ansible Modules

To get information on the available Ansible modules, you can refer to the official documentation or run the following commands in your terminal:

### General Information on Modules

```bash
ansible-doc -l  # This will retrieve a list of all available modules

ansible-doc <Module_Name>  # Replace <Module_Name> with the name of the module
```

finally how to run the playbook

``` sh
 ansible-playbook playbook.yml
```

## Verifying Playbooks




