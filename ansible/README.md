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
client1 ansible_host=10.196.16.44 ansible_user=root ansible_ssh_private_key_file=/root/.ssh/id_rsa

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
[group1_]

client1
client2

[group_2]

client3
client4

```

#### Файл ansible.cfg не является обязательным, запустить ansible можно так же используя только файл host

```

# ansible all -i host -m ping
```


#### Так же можно запускать конкретные хосты или группы хостов прописанные в файле host

```

# ansible [имя_хоста] -i host -m ping

# ansible [имя_группы] -i host -m ping
```


## Команды и модули

#### вывести подробную информацию о клиентах

```

# Ansible [имя_хоста или all] -i host -m setup
```


#### Выполнить комнадну на клиенте (на примере uptime)

```
# Ansible [имя_хоста или all] -i host -m shell -a "uptime"

Более целесообразно использовать модуль command, а не shell

# Ansible [имя_хоста или all] -i host -m command -a "uptime"

еще пример: создаем файл

# ansible client1 -m file -a "path=/home/oracle/ansible-file.txt state=touch"

```

## Переменные

Параменты в файле host, можно задать при помощи переменных, для этого:

```

Создаем директорию group_vars

  # mkdir group_vars

Переходим в директорию и создаем файл с именем группы

  # touch group_1

открываем файл в текстовом редакторе

  # vi group_1

в данный файл мы прописываем переменные которые буду приментся к хостам в host

ansible_host: 10.196.16.44
ansible_user: root
ansible_ssh_private_key_file: /root/.ssh/id_rsa


p.s. для того что бы работало, в файле host должны быть созданы группы
```

```
# Ansible [имя_хоста или all] -i host -m setup

```


## Playbook


#### Создаем файл с именем Playbookа, и расширением .yml, например

```

 # touch ping.yml

со следующим содержимым

- name: Ping Servers
  hosts: all
  become: yes

  tasks:

  - name: Task ping
    ping:

p.s. Обязательно придерживаться формата!!

```


#### Переменные в плейбуке

```
переменные задаются внутри плейбука в vars:

   vars:
     имя_переменной:
                    - значение_1
                    - значение_2

вызываются переменные при момощи заключение в "{{}}"

"{{имя_переменной}}"

```

#### Циклы

```
В данном примере на всех хостах прописанных в host будут созданы директории dir1 dir2 по пути /home

- name: Loops
  hosts: all
  become: yes

  tasks:

  - name: Create Folder
    file:
      path: "/home/{{item}}"
      state: directory
    loop:
      - dir1
      - dir2

т.е. сколько значений будет в loop, столько и будет циклов

```
