# devops-netology
Домашная работа "Операционные системы (лекция 2)"

1. 
- Был сконфигурирован init-файл для node_exporter
  vagrant@vagrant:~$ cat /etc/systemd/system/node_exporter.service
  [Unit]
  Description=Node Exporter

  [Service]
  ExecStart=/usr/local/bin/node_exporter $OPTIONS
  EnviromentalFile=/etc/default/node_exporter

  [Install]
  WantedBy=multi-user.target

- Далее node_exporter  помещен в автозагрузку
  vagrant@vagrant:~$ sudo systemctl daemon-reload
  vagrant@vagrant:~$ sudo systemctl enable node_exporter
  vagrant@vagrant:~$ systemctl start node_exporter
- После перезагрузки виртуальной машинки
  vagrant@vagrant:~$ ps -e | grep node_exporter
    649 ?        00:00:00 node_exporter
  vagrant@vagrant:~$ systemctl stop node_exporter
  ==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
  Authentication is required to stop 'node_exporter.service'.
  Authenticating as: vagrant
  Password:
  ==== AUTHENTICATION COMPLETE ===
  vagrant@vagrant:~$ ps -e | grep node_exporter
  vagrant@vagrant:~$ systemctl start node_exporter
  ==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
  Authentication is required to start 'node_exporter.service'.
  Authenticating as: vagrant
  Password:
  ==== AUTHENTICATION COMPLETE ===
  vagrant@vagrant:~$ ps -e | grep node_exporter
   1379 ?        00:00:00 node_exporter

2.
CPU(для каждого cpu)
node_cpu_seconds_total{cpu="0",mode="system"} 9.37
node_cpu_seconds_total{cpu="0",mode="user"} 4.78
process_cpu_seconds_total 0.15
Memory
node_memory_MemAvailable_bytes
node_memory_MemTotal_bytes
Disk
node_disk_read_bytes_total
node_disk_read_time_seconds_total
node_disk_write_time_seconds_total
Network
node_network_receive_bytes_total{device="eth0"} 341321
node_network_receive_errs_total{device="eth0"} 0
node_network_transmit_bytes_total{device="eth0"} 309777
node_network_transmit_errs_total{device="eth0"} 0

3.
- Установлен Netdata, добавлена секция с записью в файл etc/netdata/netdata.conf
  [web]
  bind to = 0.0.0.0
- Проброшен порт Netdata в локальный ПК путем добавления записи 
  config.vm.network "forwarded_port", guest: 19999, host: 19999
  в файл Vagrantfile.
- Ознакомился с метриками по умолчанию. Ссылку на скриншот приложил к домашней работе.

4.
Да, возможно путем выполнения 
vagrant@vagrant:~$ sudo dmesg | grep "Hypervisor detected"
[    0.000000] Hypervisor detected: KVM
На реальной железке данная запись бы отсутствовала.
Также были найдены следующие строки:
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.154320] Booting paravirtualized kernel on KVM

5.
fs.nr_open это системное ограничение количества открытых дескриптеров
vagrant@vagrant:~$ /sbin/sysctl -n fs.nr_open
1048576

Также существует так называемый “Жесткий лимит”, его можно узнать здесь
vagrant@vagrant:~$ ulimit -Hn
1048576

6.
vagrant@vagrant:~$ ps -e | grep sleep
   1568 pts/0    00:00:00 sleep
vagrant@vagrant:~$ sudo -i nsenter -t 1568 -p -m
root@vagrant:/# ps
    PID TTY          TIME CMD
   1579 pts/1    00:00:00 sudo
   1581 pts/1    00:00:00 nsenter
   1586 pts/1    00:00:00 bash
   1597 pts/1    00:00:00 ps

7. 
Если интерпретировать :(){ :|:& };: в более удобоваримый вид, то получаем
f {
    f | f &
}; f
Те определение функции, которая вызывает сама себя, причем два экземпляра. Далее процесс повторяется и так до бесконечности.

dmesg показал следующее, если не ошибаюсь. то это строка относится к нашему случаю. 
[ 1506.078085] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-7.scope

Механизм работает похоже следующим образом, он отслеживает количество созданных процессов, соответственно при достижении заданного ограничения, блокирует дальнейшее создание процессов.
Максимальное число пользовательских процессов можно настроить с помощью ulimit -u.
