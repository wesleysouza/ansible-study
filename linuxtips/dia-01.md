# Dia 01

## Instalando o Ansible

[Documentação: instalação](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

Comandos:

```bash
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```

Existem arquivos importantes na pasta **/etc/ansible**:
- ansible.cfg: arquivo de configuração;
- hosts: inventário com os hosts que vão ser configurados;
- roles.

Crie uma pasta e crie dentro dele o arquivo hosts.

Obs.: é possível acessar as máquinas "cliente" pelo nome adicionando ip e nome das máquinas no arquivo **/etc/hosts**.

## Setup inicial

A primeira coisa que devemos fazer é observar se é possível se conectar via ssh nas máquinas clientes.

Na AWS basta passar o arquivo .pem das máquinas clientes para a máquina do ansible. Exemplo:

```bash
scp -i ansible-class.pem ansible-class.pem usuario@maquina:/tmp
```

Conectando:

```bash
ssh -i arquivo.pem ip
```

### Gerando chave ssh (relação de confiança)

```bash
ssh-keygen
```

Passando a chave pública para os clientes na pasta:

```bash
ssh-copy-id usuario@ip_ou_nome
```

Fonte:
- [Copying SSH Keys To Another Machine](https://www.baeldung.com/linux/copy-ssh-keys)

### ssh agent (Caso AWS)

Caso a chave não siga o nome padrão é possível configurar um agent para não ser necessário passar o arquivo com o parâmetro -i.

```bash
ssh-agent bash
ssh-add ansible-class.pem
ssh ip
```

### Primeiro ping

Crie o arquivo hosts onde será escrito o nosso inventário.

Comandos ad doc:
- Basicamente é pedir para o ansible executar um comando nas máquinas clientes.

Playbook: Código que descreve um cojunto de tarefas que devem ser executadas.

O nosso primeiro comando ansible:

```bash
ansible -i hosts all -m ping
```

Observações:
- -i: pra passar o nome do arquivo de inventário;
- all: rodar em todas as máquinas;
- m: módulo (formas e executar tarefas nas máquinas alvo).

## Trabalhando com os módulos

[Documentação: Module Index](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

### Utilizando o módulo apt

```bash
ansible -i hosts ansible-node-1 -m apt -a "name=cmatrix state=present" -bK
```

Observações:
- -a: argumentos
- -bK: executar como sudo pedindo a senha.

[Ansible troubleshooting - missing sudo password and incorrect sudo password](https://www.ansiblepilot.com/articles/ansible-troubleshooting-missing-sudo-password/)

Caso a lista de funte dos pacotes não esteja atualizada pode dar problema. Para fazer essa atualização antes de rodar adicione o update_cache=yes no comando:

```bash
ansible -i hosts ansible-node-1 -m apt -a "update_cache=yes name=cmatrix state=present" -bK
```

[Documentação: apt – Manages apt-packages](https://docs.ansible.com/ansible/2.9/modules/apt_module.html#apt-module)

### Usando o módulo shell

```bash
ansible -i hosts ansible-node-1 -m shell -a "ip a"
```

### Usando o módulo copy

```bash
ansible -i hosts ansible-node-1 -m copy -a "src=giropops dest=/tmp"
```

### Usando o módulo git

Esse módulo faz parte dos módulos de controle de versão disponíveis em:

[Source Control modules](https://docs.ansible.com/ansible/2.9/modules/list_of_source_control_modules.html)

```bash
ansible -i hosts ansible-node-1 -m git -a "repo=nome_do_repo dest=/home/k8s/nome-repositorio"
```

### Usando o módulo setup

Pegando os ansible facts, as variáveis do ansible. Sendo assim, vamos ver toda a configuração das máquinas.

```bash
ansible -i hosts ansible-node-1 -m setup
```

É possível filtrar a saída, por exemplo, obtendo a distribuição das máquinas:

```bash
ansible -i hosts all -m setup -a "filter=ansible_distribution"
```

Vendo o nome dos nós:

```bash
ansible -i hosts all -m setup -a "filter=ansible_nodename"
```

Obtendo informações sobre o ip:

```bash
ansible -i hosts all -m setup -a "filter=ansible_default_ipv4" | more
```

## Criando o primeiro playbook

Sempre que fornecessário configuração e instalaçaõ de pacotes crie um playbook.

Crie o arquivo **meu_primeiro_playbook.yml**:

```yml
---
- hosts: webservers
  become: yes
  remote_user: k8s
  tasks:
  - name: Instalando o Nginx
    apt:
      name: nginx
      state: present
      update_cache: yes
  
  - name: Iniciando o Nginx
    service:
      name: nginx
      enabled: yes
  
  - name: Iniciando o Nginx
    service:
      name: nginx
      state: started
```

Executando:

```
ansible-playbook -i hosts meu_primeiro_playbook.yml
```

É possíver **definir grupos** no arquivo de inventário, exemplo:

```
[webservers]
ansible-node-1

[db]
ansible-node-2
```

### Adicionando arquivo personalizado no nginx

Criando o arquivo:

```html
<html>
    <head>
        <title>TESTE</title>
    </head>

    <body>
        <h1>TESTE NGINX</h1>
    </body>
</html>
```

Editando o arquivo **meu_primeiro_playbook.yml**:

```yml
---
- hosts: webservers
  become: yes
  remote_user: k8s
  tasks:
  - name: Instalando o Nginx
    apt:
      name: nginx
      state: present
      update_cache: yes
  
  - name: Iniciando o Nginx
    service:
      name: nginx
      enabled: yes
  
  - name: Iniciando o Nginx
    service:
      name: nginx
      state: started

  - name: Copiando HTML personalizado
    copy:
      src: index.html
      dest: /var/www/html/index.html
```

Execute novamente:

```
ansible-playbook -i hosts meu_primeiro_playbook.yml
```

Obs.: Se for alterado o conteúdo do arquivo o ansible envia a cópia novamente.

### Handlers

Handlers são utlizados para tomar alguma ação.

Criando o handler:

```yml
- name: Copiando nginx.conf
  copy:
    src: index.html
    dest: /var/www/html/index.html
  notify: Restartando o Nginx

handlers:
- name: Restartando o Nginx
  service:
    name: nginx
    state: restarted
```

Obs.: O campo **notify** é quem chama o **handler**.

### Desigando as máquinas

```yml
- hosts: all
  become: yes
  remote_user: k8s
  tasks:
  - name: Desigando clientes
    shell: shutdown
```