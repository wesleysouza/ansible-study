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

```
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
```
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
