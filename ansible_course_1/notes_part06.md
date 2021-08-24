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

### Command modules are used to execute commands or scripts on a host.

Executes a command on a remote node

| Parameter | Comments                |
|-----------|-------------------------|
| chdir     | cd into this directory before running the command |
