# Домашняя работа к занятию "08.01 Введение в Ansible"

- Где расположен файл с 'some_fact' из второго пункта задания?
group_vars/all/example.yml
- Какая команда нужна для запуска вашего 'playbook' на окружении 'test.yml'?
ansible-playbook -i inventory/test.yml site.yml
- Какой командой можно зашифровать файл?
ansible-vault encrypt data.txt
- Какой командой можно расшифровать файл?
ansible-vault decrypt data.txt
- Можно ли посмотреть содержимое зашифрованного файла без команды расшифровки файла? Если можно, то как?
Можно с помощью команды 'ansible-vault viev data.txt'
- Как выглядит команда запуска 'playbook', если переменные зашифрованы?
ansible-playbook -i inventory/test.yml site.yml --ask-vault-pass
- Как называется модуль подключения к host на windows?
winrm
- Приведите полный текст команды для поиска информации в документации ansible для модуля подключений 'ssh'
ansible-doc -t connection ssh
- Какой параметр из модуля подключения 'ssh' необходим для того, чтобы определить пользователя, под которым необходимо совершать подключение?
- remote-user()

Ссылка на [playbook](https://github.com/everoff/devops-netology/playbook) 
