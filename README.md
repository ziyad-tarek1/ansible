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

### Verifying Ansible Playbooks

You can verify Ansible playbooks in three different modes:

#### 1. Check Mode ("Dry Run")

Check mode allows you to simulate the execution of a playbook without making any changes to the target hosts. This is useful for understanding what changes would be made.

```sh
ansible-playbook playbook.yml --check
```
Note: Not all Ansible modules support check mode. If a module does not support this mode, it will be skipped during the dry run operation.

#### 2. Diff Mode
Diff mode provides a before-and-after comparison of the changes that would be made by the playbook. This helps you understand and verify the impact of playbook changes before applying them.
  ``` sh
 ansible-playbook playbook.yml --check --diff
```
#### 3. Syntax Check
In this mode, Ansible only checks the syntax of the playbook to ensure it is error-free. This is useful for catching syntax errors before execution.

  ``` sh
 ansible-playbook playbook.yml  --syntax-check
```

## Ansible-lint✨

  * Ansible Lint is a command-line tool that performs linting on Ansible playbooks, roles, 
  and collections. 
  * It checks your code for potential errors, bugs, stylistic errors, and suspicious 
  constructs. 
  * It's akin to having a seasoned Ansible mentor guiding you, providing valuable 
  insights, and catching issues that might have slipped past your notice.

``` bash
  ansible-lint style_example.yml
```


## Ansible Conditionals [when] | Ansible Operators [ or | and ]

if you have a playbook with a task spacific to a condition you have ansible conditions come in handy
1- Conditional - when: 
only if the when condition is true this task will run

``` yaml 
---
- name: Install NGINX
  hosts: all
  tasks:
    - name: Install NGINX on Debian
      apt:
        name: nginx
        state: present
      when: ansible_os_family == "Debian"

    - name: Install NGINX on RedHat or SUSE
      yum:
        name: nginx
        state: present
      when: ansible_os_family == "RedHat" or ansible_os_family == "SUSE"
 ```

 ``` yaml
---
- name: Install NGINX
  hosts: all
  tasks:
    - name: Install NGINX on Debian 16.04
      apt:
        name: nginx
        state: present
      when: ansible_os_family == "Debian" and ansible_distribution_version == "16.04"

    - name: Install NGINX on RedHat or SUSE
      yum:
        name: nginx
        state: present
      when: ansible_os_family == "RedHat" or ansible_os_family == "SUSE"
 ```
Make sure to use double equal sing ( == ) when checking equality of a statment

### Conditionals in Loops

``` yaml
---
- name: Install Softwares
  hosts: all
  vars:
    packages:
      - name: nginx
        required: True
      - name: mysql
        required: True
      - name: apache
        required: False

  tasks:
    - name: Install "{{ item.name }}" on Debian
      apt:
        name: "{{ item.name }}"
        state: present
      when: item.required == True
      loop: "{{ packages }}"
```

### Conditionals & Register-name: 
- Check status of a Register and take an action based on it

``` yaml
---
- name: Check status of a service and email if it's down
  hosts: localhost
  tasks:
    - name: Check httpd service status
      command: service httpd status
      register: result

    - name: Send email alert if httpd service is down
      mail:
        to: admin@company.com
        subject: "Service Alert"
        body: "Httpd Service is down"
      when: result.stdout.find('down') != -1

```

## Ansible Loops

There are two types of initializing a loop in Ansible:

1. **First**: Using `loop`
2. **Second**: Using `with_*`

Actually, loops were recently added to Ansible. Let's take an example: if you have a list of users and UIDs and you want to use the `user` module to add them, you can define it as a list of dictionaries and call the items in this loop using `{{ item.key }}`.

### Example 1: Using `loop`
EX:
```yaml
---
- name: Create users
  hosts: localhost
  tasks:
    - name: Create users
      user:
        name: "{{ item.name }}"
        state: present
        uid: "{{ item.uid }}"
      loop:
        - { name: joe, uid: 1010 }
        - { name: george, uid: 1011 }
        - { name: ravi, uid: 1012 }
        - { name: mani, uid: 1013 }
        - { name: kiran, uid: 1014 }
        - { name: jazlan, uid: 1015 }
        - { name: emaan, uid: 1016 }
        - { name: mazin, uid: 1017 }
        - { name: izaan, uid: 1018 }
        - { name: mike, uid: 1019 }
```
### Example 2: Using a different format

```yaml
---
- name: Create users
  hosts: localhost
  tasks:
    - name: Create users
      user:
        name: "{{ item.name }}"
        state: present
        uid: "{{ item.uid }}"
      loop:
        - name: joe
          uid: 1010 
        - name: 
          uid: 

```


### Notes:
- The first example uses a list of dictionaries with inline syntax.
- The second example uses a more structured format for defining the users and their UIDs.

#### What is `with_*`

In Ansible, `with_*` is a way to initialize loops that allows you to iterate over various types of data sources. This feature provides a more specialized looping mechanism for specific use cases. Here are some common `with_*` constructs:

- **`with_items`**: This is used to loop over a list of items. It is one of the most commonly used loop constructs in Ansible. For example, you can use it to iterate over a list of users or packages.

    ```yaml
    - name: Install packages
      yum:
        name: "{{ item }}"
        state: present
      with_items:
        - httpd
        - mysql
        - nginx
    ```

- **`with_file`**: This is used to loop over files in a directory. It allows you to perform actions on each file found in a specified path.

    ```yaml
    - name: Copy files to remote server
      copy:
        src: "{{ item }}"
        dest: /destination/path/
      with_file:
        - /path/to/source/file1
        - /path/to/source/file2
    ```

- **`with_mongodb`**: This is used to loop over documents in a MongoDB collection. It allows you to perform actions on each document retrieved from the database.

    ```yaml
    - name: Process MongoDB documents
      mongodb_query:
        database: mydb
        collection: mycollection
        query: {}
      register: mongo_results

    - name: Do something with each document
      debug:
        msg: "{{ item }}"
      with_items: "{{ mongo_results.documents }}"
    ```

- **`with_url`**: This is used to loop over a list of URLs. It allows you to download files or perform actions on each URL in the list.

    ```yaml
    - name: Download files from URLs
      get_url:
        url: "{{ item }}"
        dest: /path/to/destination/
      with_url:
        - http://example.com/file1
        - http://example.com/file2
    ```

### Summary

The `with_*` constructs in Ansible provide a flexible way to iterate over different types of data sources, making it easier to perform repetitive tasks in your playbooks. Each `with_*` construct is tailored for specific use cases, allowing for more efficient and readable playbook code.
and the * is a lookup plugin that you can use to loop

#### lookup plugin is custom scripts that can do spacific task like read file or connect to DB or kubernetese 


## Ansible Plugins

- A piece of code that modifies the functionality of Ansible.
- Enhances various aspects of Ansible such as **Inventory**, **Modules**, and **Callbacks**.
- A flexible and powerful way to customize Ansible behavior.

Ansible plugins can take the form of:

- **Inventory Plugin**: Used to manage and customize inventory sources. This allows you to define how Ansible retrieves and organizes hosts.

- **Module Plugin**: When you develop a custom module plugin, you can create a custom cloud resource with specific configurations tailored to your needs.

- **Action Plugin**: This type of plugin allows you to define custom actions that can be executed during a playbook run. Action plugins can modify the behavior of tasks and provide additional functionality, such as handling task execution in a specific way or managing task dependencies.

- **Callback Plugin**: Callback plugins are used to customize the output of Ansible runs. They can modify how results are displayed, log output, or send notifications based on the results of playbook execution.

### Examples of Plugins

- **Dynamic Inventory Plugin**: This enables you to fetch real-time data for inventory, for example, from your cloud provider's API. This is particularly useful for environments where hosts are frequently changing, such as in cloud environments.

- **Custom Module Plugin**: If you need to interact with a specific API or service that is not covered by existing Ansible modules, you can create a custom module plugin to handle those interactions.

- **Custom Action Plugin**: You might create an action plugin to implement a specific workflow that involves multiple tasks or to enforce certain conditions before executing tasks.

- **Custom Callback Plugin**: For example, you could create a callback plugin that sends an email notification whenever a playbook run completes, summarizing the results.

### Summary

Ansible plugins provide a powerful mechanism to extend and customize the functionality of Ansible, allowing users to tailor their automation workflows to meet specific requirements.


## Introduction to Handlers✨

-  Tasks triggered by events/notifications.
-  Defined in playbook, executed when notified by a task
-  Manage actions based on system state/configuration changes

```yaml
---
- name: Deploy Application
  hosts: application_servers
  tasks:
    - name: Copy Application Code
      copy:
        src: app_code/
        dest: /opt/application/
      notify: Restart Application Service

  handlers:
    - name: Restart Application Service
      service:
        name: application_service
        state: restarted
```

## Ansible Roles

in ansible configuration dir "/etc/ansible/ansible.cfg" the defualt path of roles is defined in

```sh
roles_path = /etc/ansible/roles
```
However, you can create an Ansible Galaxy directory under the roles directory to maintain your Ansible tasks in an organized manner. This allows you to structure your roles effectively and makes it easier to manage complex playbooks.

```sh
my_role/
├── tasks/
│   └── main.yml
├── handlers/
│   └── main.yml
├── templates/
│   └── my_template.j2
├── files/
│   └── my_file.txt
├── vars/
│   └── main.yml
├── defaults/
│   └── main.yml
└── meta/
    └── main.yml
```
### To fined roles from ansible galaxy community you can search online in the ansible galaxy GUI or using the below command 
```sh
ansible galaxy search <key word ex: mysql>
ansible galaxy install <role name> 
```
after the installintion the role will be downloaded in /etc/ansible/roles you can use -p </path/where/you/want/to/save/it>

you can list all the existing roles in your system by using
```sh
ansible galaxy list
```

## Ansible Collections ✨

- **Package and Distribute**: Ansible Collections are used to package and distribute modules, roles, plugins, and other Ansible content.
- **Self-Contained**: Collections are self-contained, meaning they can include everything needed for a specific functionality or integration.
- **Community and Vendor-Created**: Collections can be created by both the community and vendors, allowing for a wide range of available content.

### Example of Installing a Collection

You can install an Ansible collection using the `ansible-galaxy` command. For example, to install the `amazon.aws` collection, you would run:

```sh
ansible-galaxy collection install amazon.aws
```
```yaml

---
- hosts: localhost
  collections:
    - amazon.aws
  tasks:
    - name: Create an S3 bucket
      aws_s3_bucket:
        name: my-bucket
        region: us-west-1
        state: present
```

### Introduction to Templating

Using a template in Ansible allows you to create configuration files where user-defined inputs are stored in a variable file. You can reference these variables by their keys in the configuration file, making it easy to manage configurations dynamically.

#### Example

- **Template**: This is the configuration file template that uses Jinja2 syntax to reference variables.

```yaml
[mysqld]
innodb-buffer-pool-size={{ pool_size }}
datadir={{ datadir }}
user={{ mysql_user }}
symbolic-links={{ link_id }}
port={{ mysql_port }}
```
-Variables: This is a separate YAML file that defines the variables used in the template.
```yaml
pool_size: 5242880
datadir: /var/lib/mysql
mysql_user: mysql
link_id: 0
mysql_port: 3306
 ```
 - Output: When the template is rendered with the provided variables, the output will look like this:
 ```yaml
[mysqld]
innodb-buffer-pool-size=5242880
datadir=/var/lib/mysql
user=mysql
symbolic-links=0
port=3306
 ```

 Explanation
Template File: The template file uses Jinja2 syntax (e.g., {{ variable_name }}) to insert variable values into the configuration file dynamically.

Variables File: The variables file contains key-value pairs that define the values for the placeholders in the template.

Rendered Output: When the template is processed by Ansible, it replaces the placeholders with the actual values from the variables file, resulting in a complete configuration file.


### Notes:
- The example provided illustrates how to use Jinja2 templating in Ansible to create configuration files dynamically.
- Templating is particularly useful for managing configurations in environments where settings may change frequently or differ between deployments.

Templating in Ansible is a powerful feature that allows you to create dynamic configuration files based on user-defined variables. This approach enhances flexibility and maintainability, making it easier to manage configurations across different environments.

### Jinja2

Jinja2 is a powerful templating engine for Python that allows for dynamic content generation. It provides features for string manipulation, filters, loops, and conditions.

#### String Manipulation | Filters

You can manipulate strings using filters in Jinja2. Here are some examples:

- **Basic Variable Usage**:
  ```jinja
  The name is {{ my_name }}  => The name is Bond
  The name is {{ my_name| upper }} => The name is BOND
  The name is {{ my_name| lower }} => The name is bond
  The name is {{ my_name| title }} => The name is Bond
  The name is {{ my_name| replace (“Bond”, “Bourne”) }} => The name is Bourne
  The name is {{ first_name| default(“James”) }} {{ my_name}} => The name is James Bond
  ```

- **Filters -List and set**
```jinja
 {{ [ 1, 2, 3 ] | min }}            => 1
 {{ [ 1, 2, 3 ] | max }}                  => 3
 {{ [ 1, 2, 3, 2 ] | unique }}              => 1, 2, 3
 {{ [ 1, 2, 3, 4 ] | union( [ 4, 5 ] ) }}     => 1, 2, 3, 4, 5
 {{ [ 1, 2, 3, 4 ] | intersect( [ 4, 5 ] ) }}  => 4
 {{  100 | random }}  => Random number
 {{ [“The”, “name”, “is”, “Bond”] | join(“ “) }}  => The name is Bond
```
 - **Loops**
```jinja
  {% for number in [0,1,2,3,4] %}
 {{ number }}
 {% endfor %}
```

 - **Conditions**

```jinja
 {% for number in [0,1,2,3,4] %}
 {% if number == 2 %}
 {{ number }}
 {% endif %}
 {% endfor %}
 ```

 ------------------------------------------------------------------------------------

 # Ansible Advanced 

 ## Playbook run options

1- dry run option 
```yaml
ansible-playbook play1.yaml --check
```

2- start at option

``` yaml
ansible-playbook play1.yaml --start-at-task "task name"

```

3- tags

* you can add tags to your tasks and call it when you run your playbook

```yaml 
ansible-playbook play.yaml --tag "hashtag"
```

* Or use it to skip a certine task taged 

```yaml
asnible-playbook --skip-tag "hashtag"
```

## Facts
To print Facts from the ansibe playbook

```yaml
---
- name: Print hello message
  hosts: all
  tasks:
    - name: Display ansible_facts
      debug:
        var: ansible_facts
```

to chang this behaviour you can add gather_facts: no in the playbook or change the defualt configuration at /etc/ansible/ansible.cfg to implicit

**Alternatively, you can set the environment variable ANSIBLE_GATHERING to explicit:**
export ANSIBLE_GATHERING=explicit


## Create and distribute SSH keys to managed nodes

#### Step 1: Generate SSH Keys

First, you need to generate an SSH key pair on your Ansible control machine (the machine from which you will run Ansible). You can do this using the following command:

```sh
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -C "your_email@example.com"
```

#### Step 2: Copy the Public Key to Managed Nodes

Once you have generated the SSH key pair, you need to copy the public key (~/.ssh/id_rsa.pub) to each managed node. You can use the ssh-copy-id command for this purpose:

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub user1@172.20.1.100
ssh-copy-id -i ~/.ssh/id_rsa.pub user1@172.20.1.101
```
This command will prompt you for the password of user1 on each managed node. After entering the password, the public key will be added to the ~/.ssh/authorized_keys file on the managed nodes.

#### Step 3: Verify SSH Access

After copying the public key, you should verify that you can SSH into the managed nodes without being prompted for a password:

```sh
ssh user1@172.20.1.100
ssh user1@172.20.1.101
```

Step 4: Update Ansible Inventory
Now that you have SSH access set up, you can use the following Ansible inventory configuration to manage your nodes:

```sh
[web]
web1 ansible_host=172.20.1.100 ansible_user=user1 ansible_ssh_private_key_file=/some-path/private-key
web2 ansible_host=172.20.1.101 ansible_user=user1 ansible_ssh_private_key_file=/some-path/private-key
```

##### Explanation of Inventory Configuration:
* Group Header: The line [web] defines a group called web.
* Host Definitions: Each host (web1 and web2) is defined on a single line, followed by the necessary variables.
* Variables:
  * ansible_host: The IP address of the managed node.
  * ansible_user: The username used to connect to the managed node.
  * ansible_ssh_private_key_file: The path to the private key file used for SSH authentication.

#### Step 5: Run Ansible Playbooks
You can now run Ansible playbooks targeting the web group. For example, to check connectivity, you can use:
```bash
ansible -i /etc/ansible/hosts web -m ping
```

## Validate a working configuration using ad-hoc Ansible commands

Ansible ad-hoc commands provide a quick and easy way to test your Ansible setup or run a single module on one or more target hosts. This is particularly useful for validating connectivity and executing simple tasks without the need to create a full playbook.

1- Ping Command: The ping module is a simple way to check if the target host is reachable. It does not use ICMP ping; instead, it checks if Ansible can connect to the host.

```sh
ansible -m ping target1
```
Replace target1 with the name of your target host or group defined in your inventory.
If the command is successful, you should see a response indicating that the host is reachable.

2- Run a Command: You can use the command module to run any command on the target host. For example, to display the contents of the /etc/resolv.conf file, you can use
```sh
ansible -a 'cat /etc/resolv.conf '

```
This command will execute cat /etc/resolv.conf on target1 and display the output in your terminal.


3- Gather System Information: You can gather system information using the setup module:

```sh
ansible -m setup target1
```

4- To ping all hosts defined in a specific inventory file and redirect the output to a text file, you can use:

```sh
ansible -m ping -i /home/thor/playbooks/inventory all > /tmp/ansible_all.txt
```
5- To run a command on a specific host (e.g., web1) and save the output to a text file, you can use:
```
ansible -m command -a date -i inventory web1 > /tmp/ansible_date.txt
```

## Privilege Escalation
Privilege escalation in Ansible allows you to run tasks with elevated privileges, such as installing software or modifying system configurations. This is commonly done using sudo, but Ansible supports various methods for privilege escalation.



1-  Become Super user (sudo):
To run tasks as a superuser (using sudo), you can use the become directive in your playbook. Here’s an example of how to install nginx:

```yaml
---
- name: Install nginx
  become: yes
  hosts: all
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: latest
 ```
2- Become Method – sudo (pfexec, doas, ksu, runas):
You can specify the method of privilege escalation using the become_method directive. For example, if you want to use doas instead of sudo, you can do it like this:


```yaml
---
- name: Install nginx
  become: yes
  become_method: doas
  hosts: all
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: latest
```
3-  Become another user
You can also run tasks as a different user by specifying the become_user directive. For example, if you want to install nginx as the user nginx, you can configure your inventory and playbook as follows:


```sh
# inventory
lamp-dev1 ansible_host=172.20.1.100 ansible_user=admin ansible_become=yes ansible_become_user=nginx```


```yaml
---
- name: Install nginx
  become: yes
  become_user: nginx
  hosts: all
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: latest
```
4- Configuration File:
The Ansible configuration file (ansible.cfg) allows you to set default behaviors for Ansible, including privilege escalation settings. You can specify the default method of privilege escalation and the user to become.

```sh
[defaults]
# Enable privilege escalation by default
become = True

# Set the default method for privilege escalation
become_method = doas

# Set the default user to become
become_user = nginx

# Specify the inventory file location (if needed)
inventory = /path/to/your/inventory
```

5- comand line argument
```sh
 ansible-playbook --become--become-method=doas--become-user=nginx--ask-become-pass
```


## Additional Modules

#### Packages & Service


```yaml
---
- name: Install web on CentOS
  hosts: all
  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: installed
```

```yaml
---
- name: Install web on Ubuntu
  hosts: all
  tasks:
    - name: Install apache2
      apt:
        name: apache2
        state: installed
```

```yaml
---
- name: Install web on Any Host
  hosts: all
  tasks:
    - name: Install httpd or apache2
      package:  # This module will use the host's package manager, whether it is CentOS or Ubuntu (yum or apt)
        name: 
          - httpd   # Be careful with the name; some Linux distributions call it httpd, while others call it apache2
          - apache2
        state: installed
        enabled: yes  # Ensure that the service will be enabled on the next boot
```

#### Firewall Rules

```yaml
---
- name: Add Firewalld rule
  hosts: all
  tasks:
    - name: Allow HTTP traffic on port 8080
      firewalld:
        port: 8080/tcp
        service: http
        source: 192.0.0.0/24
        zone: public
        state: enabled
        permanent: yes
        immediate: yes
```


#### Storage

```yaml

---
- name: Manage LVM Storage
  hosts: all
  tasks:
    - name: Create LVM Volume Group
      lvg:
        vg: vg1
        pvs: /dev/sdb1,/dev/sdb2

    - name: Create LVM Logical Volume
      lvol:
        vg: vg1
        lv: lvol1
        size: 2g

```


#### File System

```yaml

---
- name: Manage LVM Storage
  hosts: all
  tasks:
    - name: Create Filesystem
      filesystem:
        fstype: ext4
        dev: /dev/vg1/lvol1

    - name: Mount Filesystem
      mount:
        fstype: ext4
        src: /dev/vg1/lvol1
        path: /opt/app
        state: mounted
```

#### File

```yaml

---
- name: Create Directory and File
  hosts: all
  tasks:
    - name: Create Directory
      file:
        path: /opt/app/web
        state: directory

    - name: Create File
      file:
        path: /opt/app/web/index.html
        state: touch
        owner: app-owner
        group: app-owner
        mode: '0644
```
#### Archive

```yaml
---
- name: Compress and Uncompress a Folder
  hosts: all
  tasks:
    - name: Compress a folder
      archive:
        path: /opt/app/web
        dest: /tmp/web.gz
        format: gz  # Specify the format for compression (gz, zip, tar, bz2, xz)

    - name: Uncompress a folder
      unarchive:
        src: /tmp/web.gz # By defualt this is the src on the controller machine if you want to use a src on the target machine you should enable it at  remote_src
        dest: /opt/app/web
        remote_src: yes  # Indicates that the source file is already on the remote host
```

#### Ansible Playbook to Create a Scheduled Cron Job

```yaml
---
- name: Create a scheduled task
  hosts: all
  tasks:
    - name: Run daily health report
      cron:
        name: "Run daily health report"
        job: "/bin/sh /opt/scripts/health.sh"
        month: "2"
        day: "19"
        hour: "8"
        minute: "10"  # step value is */2 every 2 min
      # weekday: * # valuse 0 to sunday and 6 to satarday
# if you want to spacify a day use weekday 
```

#### Users and Groups

##### Ansible Playbook to Create User, Group, and Configure SSH Keys


```yaml

---
- name: Create User and Group
  hosts: all
  tasks:
    - name: Create a group
      group:
        name: developers

    - name: Create a user Maria
      user:
        name: maria
        uid: 1001
        group: developers
        shell: /bin/bash

- name: Configure SSH Keys
  hosts: all
  tasks:
    - name: Configure SSH keys for user Maria
      authorized_keys:
        user: maria
        state: present
        key: |
          ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA
          BAQC4WKn4K2G3iWg9HdCGo34gh+……root@97a1b9c3a

```



## Ansible Vault

- if you store the ansibe_host=IP and ansible_ssh_pass or ansible_password and ansible_user in the inventory data in a plain text format and this is not a best practice  you should encrypt this data using ansible vault and you have multible ways

1- if you already have an invitory file 
```bash
ansible-vault encrypt (inventory) 
```
- you will asked for the password to encrypt the file 

- if you want to use this inventory file now you sholud add the option <--ask-vault-pass> to the playbook command

```bash
 $ ansible-playbook playbook.yml–i inventory –-ask-vault-pass

 $ ansible-playbook playbook.yml–i inventory –vault-password-file ~./vault_pass.txt
 $ ansible-playbook playbook.yml–i inventory –vault-password-file ~./vault_pass.py
```

- to view the content of the encrypted file or to create it 

``` bash
 $ ansible-vault view inventory

 $ ansible-vault create inventory
```