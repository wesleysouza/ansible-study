# Variables - Part 7

Variables are used to store values that varies with different items.

Application example: run a single playbook in 100 diferent servers. Some atributes can be different as host names, usernames or passwords. 

Examples:

```yaml
variable1: value1
variable2: value2
```

```yaml
-
  name: Add DNS server to resolv.conf
  hosts: localhost
  vars:
    dns_server: 10.1.250.10
  tasks:
    - lineinfile:
        path: /etc/resolv.conf
        line: 'nameserver {{  dns_server }}' #Use of variable
```

## File for variables

The better way to do this would be to move the variables into a file in the name of the host. 

Example of variable file:
```yaml
#Sample variable File - web.yml

http_port: 8081
snmp_port: 161.162
inter_ip_range: 192.0.2.0
```

This values will be used when the playbook run in web host.

Playbook example:
```yaml
- 
  name: Set Firewall Configuration
  hosts: web
  tasks:
  - firewalld:
      service: https
      permanet: true
      state: enabled
      
  - firewalld:
      port: '{{ http_port }}'/tcp
      permanet: true
      state: enabled
      
  - firewalld:
      service: '{{ snmp_port }}'/udp
      permanet: true
      state: enabled

  - firewalld:
      service: '{{ inter_ip_range }}'/24
      permanet: true
      state: enabled
```

## Jinja2 Templating '{{    }}' 

- source: '{{ inter_ip_range}}'
- source: SomeThing{{ inter_ip_range}}SomeThing

## Exercises

### Q01

The playbook is used to update name server entry into resolv.conf file on localhost. The name server information is also updated in the inventory file as a variable nameserver_ip. Refer to the inventory file.

Replace the ip of the name server in this playbook to use the value from the inventory file, so that in the future if you had to make any changes you simply have to update the inventory file.

```yaml
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    tasks:
        -
            name: 'Update nameserver entry into resolv.conf file'
            lineinfile:
                line: 'nameserver{{nameserver_ip}}'
                path: /etc/resolv.conf
```

### Q02

We have added a new task to disable SNMP port in the playbook. However the port is hardcoded in the playbook. Update the inventory file to add a new variable snmp_port and assign the value used here. Then update the playbook to use value from the variable.

Inventory File
```yaml
localhost ansible_connection=localhost nameserver_ip=10.1.250.10 snmp_port=160-161
```

Playbook File
```yaml
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    tasks:
        -
            name: 'Update nameserver entry into resolv.conf file'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver {{ nameserver_ip }}'
        -
            name: 'Disable SNMP Port'
            firewalld:
                port: '{{snmp_port}}'
                permanent: true
                state: disabled

```

### Q03

We are printing some personal information to the screen. We would like to move the car_model, country_name and title to a variable defined at the play level.

Create three new variables (car_model, country_name and title) under the play and move the values over. Use the variables in the task.

```yaml
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    vars:
        car_model: 'BMW M3'
        country_name: 'USA'
        title: 'Systems Engineer'
    tasks:
        -
            name: 'Print my car model'
            command: 'echo "My car''s model is {{car_model}}"'
        -
            name: 'Print my country'
            command: 'echo "I live in the {{country_name}}"'
        -
            name: 'Print my title'
            command: 'echo "I work as a {{title}}"'
```
