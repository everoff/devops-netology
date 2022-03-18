# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Выполнится с ошибкой, т.к. переменные имеют разные типы  |
| Как получить для переменной `c` значение 12?  | В определении переменных надо указать a = '1'  |
| Как получить для переменной `c` значение 3?  | В определении переменных надо указать b = 2  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
mport os

bash_command = ["cd ~/Git_netology/devops-netology", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        dir = os.getcwd()
        print(dir+'/'+prepare_result)

```
Добавил в вывод недостоющую часть пути до файла с статусом modifies и убрал break, иначе мы сможем увидить только 1 файл в результате.
### Вывод скрипта при запуске при тестировании:
```
maksim@MacBook-Pro devops-netology % ./task_2.py  
/Users/maksim/Git_netology/devops-netology/Homeworks/HW_script_py.md
/Users/maksim/Git_netology/devops-netology/README.md
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

dir = input("Enter full path to repository: ");
bash_command = ["cd " + dir, "git status"]
if dir[-1] != '/':
    dir = dir+'/'
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(dir+prepare_result)
print('\n')
```

### Вывод скрипта при запуске при тестировании:
```
maksim@MacBook-Pro pythonProject % ./ex_1.py
Enter full path to repository: ~/Git_netology/devops-netology
~/Git_netology/devops-netology/Homeworks/HW_script_py.md
~/Git_netology/devops-netology/README.md


```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import datetime
import time

services = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}

while 1==1:
  for host in services:
    ip = socket.gethostbyname(host)
    print(host + ' - ' + ip)
    if ip != services[host]:
      print(str(datetime.datetime.now().strftime("%b %d %Y %H:%M:%S")) +' [ERROR] ' + host +' IP mistmatch: '+services[host]+' '+ip)
      services[host]=ip
  time.sleep(60)

```

### Вывод скрипта при запуске при тестировании:
```
maksim@MacBook-Pro devops-netology % ./task_4.py
drive.google.com - 209.85.233.194
Mar 18 2022 20:05:42 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 209.85.233.194
mail.google.com - 173.194.222.83
Mar 18 2022 20:05:42 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 173.194.222.83
google.com - 173.194.222.102
Mar 18 2022 20:05:42 [ERROR] google.com IP mistmatch: 0.0.0.0 173.194.222.102
drive.google.com - 209.85.233.194
mail.google.com - 173.194.222.83
google.com - 173.194.222.102
drive.google.com - 209.85.233.194
mail.google.com - 173.194.73.17
Mar 18 2022 20:07:42 [ERROR] mail.google.com IP mistmatch: 173.194.222.83 173.194.73.17
google.com - 173.194.222.102
```

