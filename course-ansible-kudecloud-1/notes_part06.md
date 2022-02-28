# Ansible Modules - Part 6

## Modules

The modules are categorized into various groups based on their functionality. These modules are actions to be performed at a system leval such as modifying the users and groups on the system, modifying iptables, firewall configurations on the system, working with logical volume groups, mouting operations, or working with services.

List of Modules:
- System
- Commands
- Files (ACL, Archive, Copy, File, Find, Lineinfile, Replace, Stat, Template, Unarchive ...)
- Database (Mongodb, Mssql, Mysql, Postgree ...)
- Cloud
- Windows (Win enviroment)
- E etc

### Module Command.

Executes a command on a remote node

| Parameter  | Comments                                            |
|------------|-----------------------------------------------------|
| chdir      | cd into this directory before running the command   |
| creates    | when it already exists, this step not be run        |
| executable | change the shell used to execute the command        |
| free_form  | the command module takes a free form command to run |
| removes    | a filename or glob pattern, when it does not exist, this step not run |
| warn       | ignorate warn about this particular line if set to no/false |

Example:

```yaml

-
  name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command 'date'
      command: date
    - name: Display resolv.conf contents
      command: cat /etc/resolv.conf
    - name: Display resolv.conf contents
      command: cat /resolv.conf chdir=/etc
    - name: Display resolv.conf contents
      command: mkdir /folder creates=/folder
``` 
### Module Script

Runs a local script on a remote node after transferring it. Ansible copying script to the remote node and then executing it on remote systems.

Process of the Ansible Script Module:
- Copy script to remote systems
- Excute script on remote systems

Obs.: In playbook is necessary especified path and name os script

Example:

```yaml
- 
  name: Play 1
  hosts: localhost
  tasks:
    - name: run a script on remote server
      script: /some/local/script.sh -arg1 -arg2
``` 

### Module Service

Manage Services - Start, Stop, Restart

Example 1:

```yaml
- 
  name: Start Services in Order
  hosts: localhost
  tasks:
    - name: Start the database service
      service: name=postgresql state=started
      
    - name: Start the httpd service
      service: name=httpd state=started
      
    - name: Start the nginx service
      service:
        name: nginx
        state: started
``` 

Example 2:

```yaml
- 
  name: Start Services in Order
  hosts: localhost
  tasks:
    - name: postgresql
      service:
        name: postgresql 
        state: started
``` 

#### Idempotency

An operation is idempotent if the result of performing it once is exactly the same as the result of performing it repeatedly any intervening actions.

- If httpd is not already started -> start it
- If httpd is already started -> do nothing

The overall idea is that you should be able to run the same playbook again and Ansible should report that everything is in an expected state. If something is not, Ansible takes care of putting it to the expected state.

### Module Lineinfile

Search for a line in a file and replace it or add it if it doesn't exist.

Example:

/etc/resolv.conf
```
nameserver 10.1.250.1
nameserver 10.1.250.2
``` 

add nameserver 10.1.250.10

```yaml
- 
  name: Add DNS server to resolv.conf
  hosts: localhost
  tasks:
    - lineinfile:
      path: /etc/resolv.conf
      line: 'nameserver 10.1.250.10'
``` 

Let’s compare this Ansible playbook with a simple script that tries to achieve the same results. If this script is run multiple times, it adds a new entry into the file each time, which is not desired.

If you run the Ansible playbook multiple times, it will ensure there is only a single entry in the file. Neat. Well, that’s it for now on modules in Ansible.

## Exercises

### Exercise 1

Update the playbook with a play to Execute a script on all web server nodes. The script is located at /tmp/install_script.sh

```yaml
- 
  name: Execute a script on all web server nodes
  hosts: web_nodes
  tasks:
    -  script: /tmp/install_script.sh
``` 

### Exercise 2

Update the playbook to add a new task to start httpd services on all web nodes

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Execute a script on all web server nodes'
            script: /tmp/install_script.sh
        -
            service:
                name: httpd
                state: started
```

### Exercise 3

Update the playbook to add a new task in the beginning to add an entry into /etc/resolv.conf file for hosts. The line to be added is nameserver 10.1.250.10

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            lineinfile:
                path: /etc/resolv.conf
                line: nameserver 10.1.250.10 
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present
```

[Lineinfile Module Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html)

### Exercise 4

Update the playbook to add a new task at second position (right after adding entry to resolv.conf) to create a new web user.

Use the user module for this. User details to be used are given below:
- Username: web_user
- uid: 1040
- group: developers

```yaml
-
    name: 'Execute a script on all web server nodes and start httpd service'
    hosts: web_nodes
    tasks:
        -
            name: 'Update entry into /etc/resolv.conf'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 10.1.250.10'
        -
            user:
                name: web_user
                uid: 1040
                group: developers
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present
```

[User Module Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html)

