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

  ### general example

  ```ini
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Dbp@ss123!
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
