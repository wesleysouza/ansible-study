# Ansible for Absolute Beginer - Part 3

## Ansible Inventory

O Ansible é baseado em agente. Uma das principais desvantagens de outros sistemas de automação é que eles precisam invocar algum tipo de agente.

### The Inventory File

localization -> /etc/ansible/hots

Example: investory file

```
server1.company.com
server2.company.com

[mail]
server3.company.com
server4.company.com
```

Obs.:
- definition of the group[]
- is ok create multiple groups

#### Alias

Example:
```
web ansible_host=server1.company.com
```

- ansible_host is an inventory parameter used to specify the FQDN or IP Address of a server.

#### Other Inventory Parameters:
- ansible_connection - ssh/winrm/localhost
- ansible_port - 22/5986
- ansible_user - root/administrator
- ansible_ssh_pass - Password

Example 

```
web ansible_host=server1.company.com ansible_connection - ssh ansible_user=root
web ansible_host=server2.company.com ansible_connection - winrm ansible_user=admin
```

##### Conections with diferent systems:
shh -> Linux
winrm -> WindowsServer

##### Passwords

The best practice is to set up SSH key-based passwordles authentication between the servers.

```
ansible_ssh_pass #Linux
ansible_password #Windows with winrm
```

Security: Ansible Vault

## Demo - Ansible Inventory

### Step 1: Check connectivity

Use ping and ssh for test connectivity with target host.

### Step 2: Create Project

#### Create project

```
mkdir test-project
cd test-project
cat > inventory.txt
```

Write in inventory file:

```
target1 ansible_host=ip ansible_ssh_pass=osboxes.org
target2 ansible_host=ip ansible_ssh_pass=osboxes.org
```

### Step 3: Check via Ansible command

Ping Test:
```
ansible target1 -m ping -i inventory.txt
```

#### Error in second ping test

Resolve editing /etc/ansible/ansible.cfg
Coment line: #host_key_checking

In Production enviroment using passwords to stablish connectivity between systems is not recommended.

## Conding Exercice - Ansible Inventory

###Inventory 04

```
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

[db_servers]
db1
```

### Inventory 05


```
# Sample Inventory File

# Web Servers
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

# Database Servers
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Password123!


[all_servers:children]
web_servers
db_servers

[web_servers]
web1
web2
web3

[db_servers]
db1
```

### Inventory 06

| Server Alias | Server Name   |   OS  | User          | Password |
|--------------|---------------|-------|---------------|----------|
| sql_db1      | sql01.xyz.com | Linux | root          | Lin$Pass |
| sql_db2      | sql02.xyz.com | Linux | root          | Lin$Pass |
| web_node1    | web01.xyz.com | Win   | administrator | Win$Pass |
| web_node2    | web02.xyz.com | Win   | administrator | Win$Pass |
| web_node3    | web03.xyz.com | Win   | administrator | Win$Pass |

Group the servers together based on this table

| Group        | Members                         |
|--------------|---------------------------------|
| db_nodes     | sql_db1, sql_db2                |
| web_nodes    | web_node1, web_node2, web_node3 |
| boston_nodes | sql_db1, web_node1              |
| dallas_nodes | sql_db2, web_node2, web_node3   |
| us_nodes     | boston_nodes, dallas_nodes      |

Code:

```
sql_db1 ansible_host=sql01.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass
sql_db2 ansible_host=sql02.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass
web_node1 ansible_host=web01.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass
web_node2 ansible_host=web02.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass
web_node3 ansible_host=web03.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass

[us_nodes:children]
boston_nodes
dallas_nodes

[dallas_nodes]
sql_db2
web_node2
web_node3

[boston_nodes]
sql_db1
web_node1

[web_nodes]
web_node1
web_node2
web_node3

[db_nodes]
sql_db1
sql_db2
```

