# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import datetime
import time
import json
import yaml

services = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}

while True:
  for host in services:
    ip = socket.gethostbyname(host)
    print(host + ' - ' + ip)
    if ip != services[host]:
      # To Json
      with open(host + '.json', 'a') as jsnfile:
        jsndata = json.dumps({host: ip})
        jsnfile.write(jsndata + '\n')
      # To Yaml
      with open(host + '.yml', 'a') as ymlfile:
        ymldata = yaml.dump([{host: ip}])
        ymlfile.write(ymldata + '\n')
      print(str(datetime.datetime.now().strftime("%b %d %Y %H:%M:%S")) +' [ERROR] ' + host +' IP mistmatch: '+services[host]+' '+ip)
      services[host]=ip
  time.sleep(20)
```

### Вывод скрипта при запуске при тестировании:
```
maksim@MacBook-Pro devops-netology % ./task_4.py
drive.google.com - 108.177.14.194
Mar 23 2022 20:28:50 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 108.177.14.194
mail.google.com - 142.251.1.19
Mar 23 2022 20:28:50 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 142.251.1.19
google.com - 64.233.165.100
Mar 23 2022 20:28:51 [ERROR] google.com IP mistmatch: 0.0.0.0 64.233.165.100
drive.google.com - 108.177.14.194
mail.google.com - 142.251.1.19
google.com - 64.233.165.100
drive.google.com - 108.177.14.194
mail.google.com - 142.251.1.19

```

### json-файл(ы), который(е) записал ваш скрипт:
Для каждого сервиса свой файл с расширением json, приведу пример для google.com.json
```json
{"google.com": "64.233.165.100"}
{"google.com": "64.233.165.138"}
```

### yml-файл(ы), который(е) записал ваш скрипт:
Для каждого сервиса свой файл с раширением yml, например google.com.yml
```yaml
- google.com: 64.233.165.100

- google.com: 64.233.165.138
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
#!/usr/bin/env python3

import json
import yaml
import os

filename = input('Initial file: ')

fname = os.path.splitext(filename)[0]
fext = os.path.splitext(filename)[1]

if fext == '.json':
   with open(filename) as file:
       try:
           data = json.load(file)
       except json.decoder.JSONDecodeError as err:
           print(f"Invalid JSON: {err}")
   with open(fname + '.yml', 'w') as outfile:
       outfile.write('---' + '\n')
       yaml.dump(data,outfile)
       outfile.write('...' + '\n')
       print('yaml done')
elif fext == '.yml' or fext == '.yaml':
   with open(filename) as file:
       try:
           data_yml = yaml.safe_load(file)
       except yaml.YAMLError as err:
           print(f"Invalid YAML: {err}")
   with open(fname + '.json', 'w') as outfile:
       json.dump(data_yml, outfile, indent=2)
       print('json done')
else:
   print('Incorrect file')

```

### Пример работы скрипта:
maksim@MacBook-Pro devops-netology % ./converter.py                        
Initial file: sssde
Incorrect file
maksim@MacBook-Pro devops-netology % ./converter.py
Initial file: drive.google.com.json
maksim@MacBook-Pro devops-netology % ./converter.py
Initial file: drive.google.com.json
yaml done

