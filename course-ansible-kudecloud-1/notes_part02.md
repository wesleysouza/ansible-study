# Ansible Course

## Criação do lab no VirtualBox

CentOS Machine (osboxes.org)
- user: osboxes
- pwd osboxes.org

Criar clones a partir do template:
- ansible-controller -> 192.168.0.11
- ansible-target1 -> 192.168.0.10
- ansible-target2 -> 192.168.0.12

### Executar procedimento em todas as máquinas:

#### Rename host

```bash
sudo vim /etc/hostname
Rename all hosts
```

#### Modified file hosts

```bash
sudo vim /etc/hosts
Rename -> ip localhost namehost
```

#### Restart

```bash
shutdown now -r
```

#### Install Ansible in RHEL and CentOS

```bash
sudo yum install ansible
```

