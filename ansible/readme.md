# РАБОТА c ANSIBLE по видеоурокам от [ADV-IT](https://www.youtube.com/watch?v=Ck1SGolr6GI&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N)

## 1. [Ansible](www.ansible.com) - Automation Configuration Tool

### Минимальные требования
Control Servers - Master Server:
- Linux only (RedHat, Debian, CentOS, OS x)
- Python 2.6+ или Python 3.5+

Controlled Server - Managed Servers:
- Linux: Admin UserName/Password или SSH Key и Python 2.6+
- Windows: Admin UserName/Password, PowerShell 3.0 и запустить скрипт /ConfigureRemotingForAnsible.ps1

### Работа происходит через следующие протоколы и порты:
- Linux   -> SSH Port 22  
- Windows -> WinRM Port 5986

## 2. Установка на Ubuntu и CentOS. [docs.ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html)

### CentOS
обновляем репозиторий `sudo yum install epel-release`  
устанавливаем ansible `sudo yum install ansible`  
проверяем версию `ansible --version`

### Ubuntu
добавляем репозиторий `sudo apt-add-repository ppa:ansible/ansible`  
обновляем список пакетов `sudo apt-get update`   
устанавливаем ansible `sudo apt-get install ansible`  
проверяем версию `ansible --version`

## 3. Установка на Amazon Linux через pip

устанавливаем ansible `sudo pip install ansible`  
проверяем версию `ansible --version`

## 4. Подключение к серверам Linux

### создадим 2 тачки в ВМ VirtualBox 
centos root@192.168.1.55  
ubuntu bb@192.168.1.56  
также можно было создать при помощи Vagrant `/lesson-4/Vagrantfile`  

### копирую ssh-key на созданые ВМ  
`ssh-copy-id vagrant@192.168.1.55` для каждой тачки)  

### создадим invetory файл
содержит ip адреса, user name, пароли, пути к ssh ключам и др.   
в данном случае файл `./host.txt`  
```code
[centos_server]
centos ansible_host=192.168.1.55 ansible_user=root
```

### запускаем программу на удаленой тачке  
`ansible -i hosts.txt all -m ping`  
`-i hosts.txt` - указываем на inventory файл  
`all` - запускаем на всех тачках которые указаны в `hosts.txt`  
`-m` - модуль 
`ping` - модуль который запускаем на удаленых тачках   
в ответ получаем `pong`: 
```code 
centos | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```
### создадим config файл ./ansible.cfg
```code
[defaults]
host_key_checking = false
inventory         = ./hosts.txt
```
`host_key_checking = false` - отвечает за проверку ключей при подключении по SSH. Это полезно, когда с управляющего хоста Ansible надо подключиться к большому количеству устройств первый раз, без лишних вопросов.  
`inventory = ./hosts.txt` - указываем на файл inventory, теперь его не надо указывать при вызове ansible   

### запускаем программу на нескольких тачках
добавим еще тачку в `hosts.txt`   
```code
[centos_servers]
centos ansible_host=192.168.1.55 ansible_user=root

[ubuntu_servers]
ubuntu ansible_host=192.168.1.56 ansible_user=bb
```
запускаем `ansible all -m ping`, теперь не указываем `-i hosts.txt` так как в `ansible.cfg` указали где он   
получаем результат, `"ping": "pong"`  
```code
centos | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
ubuntu | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

## 5. Подключение к серверам WINDOWS
пока не делал)))  

## 6. Правила создания файла Inventory