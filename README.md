# devops-netology
Домашная работа "Работа в терминале Лекция 1"

1. Установлен Oracle VirtualBox 6.1.28.
2. Установлен vagrant 2.2.19.
3. Был установлен алтернативный терминал iTerm2 для MacOS.
4. Создана папка vagrant, в ней файл Vagrantfile с приведеным в задании содержанием.
 Vagrant.configure("2") do |config|
 	config.vm.box = "bento/ubuntu-20.04"
 end
Запустил виртуальнаую машину путем выполнения команды vagrant up.
5. Ознакомился с интерфейсом. При создании виртуальной машины были выделены следующие аппаратные ресурсы:
CPU: 1
Processors: 2
RAM: 1024 Mb
HDD: 64 Gb
6. Добавил команды в файл Vagrantfile:
config.vm.provider "virtualbox" do |vb|
 vb.memory = "2048"
 vb.cpus = 2
end
7. Выполнено
8. 
- какой переменной можно задать длину журнала history, и на какой строчке manual это описывается? 
HISTFILESIZE - максимальное количество строк в файле истории 1131 строка;
HISTSIZE - количество команд для сохранения 1152 строка.
- что делает директива ignoreboth в bash?
ignoreboth - это сокращение для ignorespace и ignoredups.
ignorespace - исключает попадание в историю строк, начинающихся с пробела.
ignoredups - исключает запись в историю команд, если такие уже имеются в истории.
9. { } - зарезервированные слова. Используются в различных условных операторах и циклах, 208 строка.
10. 300000 создать не получится из-за превышения stack_size(по-умолчанию 8192).
11. Конструкция [[ -d /tmp ]] проверяет наличие дирректории /tmp.
12. 
vagrant@vagrant:~$ mkdir /tmp/new_path_directory/
vagrant@vagrant:~$ export PATH=$PATH:/tmp/new_path_directory/
vagrant@vagrant:~$ type -a bash
bash is /usr/bin/bash
bash is /bin/bash
vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_directory/
vagrant@vagrant:~$ PATH=/tmp/new_path_directory/:$PATH
vagrant@vagrant:~$ type -a bash
bash is /tmp/new_path_directory/bash
bash is /usr/bin/bash
bash is /bin/bash
13. at - команда, которая позволяет назначить одноразового выполнения определенной задачи в установленное время.
batch - команда, позволяющая выполнять задачи при загруженности системы ниже 0.8. Данное значение параметрируется в демоне atd.
14. Завершена работа виртуальной машины, путем выполнения команды vagrant halt.
