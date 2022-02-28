# Ansible Playbooks

Ansible playbooks are Ansible's orchestration language. Is in playbooks where we define what we want Ansible to do. It is a set of instructions you provide Ansible to work its magic.

## Playbook

- A single file written in YAML format (playbook.yml).
- Containing a set of plays. 
- A play defines a set of activities (tasks) to be run on hosts.
- A task an a single action to be performed on the host.
	- Execute a command
	- Run a script
	- Isnstall a package
	- Shutdown/Restart
	
### Levels

- Play (Playbook file can own multiple plays)
- Task

### playbook.yml example

```yaml
-  
  hosts:localhost
  name: Play 1
  tasks:
    - name: Execute command 'date'
      command: date
      
    - name: Execute script on server
      script: test_script.sh

- 
  name: Play 2
  hosts:localhost
  tasks:
   - name: Install web service
     yum:
       name: httpd
       state: present
       
   - name: Start web server
     service:
       name: httpd
       state: started 
```

#### Paramethers

- hosts: host where perform these operations (in play level). The host defined in the inventory file must match the host used in playbooks.

##Demo - Run Ansible Playbook

Commands:

```yaml
ansible-playbook playbook.yml
ansible-playbook --help
```

### Commands: ansible X ansible-playbook

ansible (simple taks, use in exceptional cases):
- ansible <hosts> -a <command>
- ansible all -a "/sbin/reboot"
- ansible <hosts> -m <module>
- ansible target1 -m ping

ansible-playbook:
- ansible-playbook <playbook name>
- 

Sintax: ansible servers-or-group -m module -i inventory-file

Obs.: ansible and ansible-playbook commands require teh inventory file specified through the -i parameter.


### Groups

- All: All servers in inventory file.

### Playbook file for pingtest

```
vim playbook-pingtest.yaml
```

Editing file

```yaml

-
  name: Test connectivity to target servers
  hosts: all
  tasks: 
    - name: Ping test
      ping: 
```

Execute the playbook file

```
ansible-playbook playbook-pingtest.yaml -i inventory.txt
```

## Demo - Tips & Tricks

Development in IDE

- Verify YAML automaticaly: www.yamllint.com
- Remote sync with Atom

Playbook for copy file (verified Ansible documentation):

```yaml

-
  name: Copy file to target servers
  hosts: all
  tasks:
    - name: Copy file
      copy:
        src: /srv/myfiles/foo.conf
        dest: /etc/foo.conf
```

Test copy file using /tmp directory.

### Idempotency concept in Ansible

Ansible only makes change if it has to. Is it's already in the same state, then it doesn't really make the change.

## Exercises

### Q3

Update the playbook to add a second task. The new task must execute the command cat /etc/hosts and change new task name to Execute a command to display hosts file

```yaml

-
    name: 'Execute two commands on localhost'
    hosts: localhost
    tasks:
        -
            name: 'Execute a date command'
            command: date
        -
            name: 'Execute a command to display hosts file'
            command: cat /etc/hosts
```

### Q5

Refer to the attached inventory file. We would like to run the tasks defined in the play on all servers in boston.

```yaml

-
    name: 'Execute two commands on web_node1'
    hosts: "web_node1,sql_db1"
    tasks:
        -
            name: 'Execute a date command'
            command: date
        -
            name: 'Execute a command to display hosts file'
            command: 'cat /etc/hosts'

```

### Q6

Create a new play named Execute a command to display hosts file contents on web_node2 to execute cat /etc/hosts command on second node web_node2 and name the task Execute a command to display hosts file.

```yaml

-
    name: 'Execute command to display date on web_node1'
    hosts: web_node1
    tasks:
        -
            name: 'Execute a date command'
            command: date
-
    name: 'Execute a command to display hosts file contents on web_node2'
    hosts: web_node2
    tasks:
        -
            name: 'Execute a command to display hosts file'
            command: cat /etc/hosts
```


### Q7

You are assigned a task to restart a number of servers in a particular sequence. The sequence and the commands to be used are given below. Note that the commands should be run on respective servers only. Refer to the inventory file and update the playbook to create the below sequence.

Note: Use the description below to name the plays and tasks.

- Stop the web services on web server nodes - service httpd stop
- Shutdown the database services on db server nodes - service mysql stop
- Restart all servers (web and db) at once - /sbin/shutdown -r
- Start the database services on db server nodes - service mysql start
- Start the web services on web server nodes - service httpd start

Warning: Do not use this playbook in a real setup. There are better ways to do these actions. This is only for simple practise.

```yaml

-
    name: 'Stop the web services on web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Stop the web services on web server nodes'
            command: 'service httpd stop'
-
    name: 'Shutdown the database services on db server nodes'
    hosts: db_nodes
    tasks:
        -
            name: 'Shutdown the database services on db server nodes'
            command: 'service mysql stop'
-
    name: 'Restart all servers (web and db) at once'
    hosts: all_nodes
    tasks:
        -
            name: 'Restart all servers (web and db) at once'
            command: '/sbin/shutdown -r'
-
    name: 'Start the database services on db server nodes'
    hosts: db_nodes
    tasks:
        -
            name: 'Start the database services on db server nodes'
            command: 'service mysql start'
-
    name: 'Start the web services on web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Start the web services on web server nodes'
            command: 'service httpd start'

```
