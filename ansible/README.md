# Installation Ansible on oracle linux 7

```

$ sudo yum install oracle-epel-release-el7

$ sudo yum install ansible

```
*p.s. для установки ansible требуется python*

#### создаем конфиг файл с следуюшим содержимым

 # vi /root/ansible.cfg

```

[defaults]

inventory = /root/ansible/host
host_key_checking = false

```

###### Проверить версию Ansible и путь до файла конфигурации можно с помощью команды

```

#  ansible --version
```

#### Настройка хостов (файла с прописаными хостнейм)

```
создаем директорию ansible в которой создаем файл с именем host в котором будем указывать клиенты

# mkdir /root/ansible

# touch /root/ansible/host

```

Далее прописываем конфиги (ip, пользователи и пароли или ключи для доступа по ssh) в файле host

 # vi /root/ansible/host

```
client1 ansible_host=10.196.16.44 ansible_user=root ansible_ssh_private_key_file=/root/.ssh/id_rsa.pub

или

client1 ansible_host=10.196.16.44 ansible_user=root ansible_password=passord (указываем пароль)

```

Проверить доступность всех хостов прописанных в файле host

```
[root@vromanov ~]# ansible all -m ping

вывод будет примрено таким:

client1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}

p.s. запускать надо из дирректории где находится файл ansible.cfg
```

#### Create ansible group

Группы хостов прописываются в файте хост

```
[group_1]

client1
client2

[group_2]

client3
client4

```
