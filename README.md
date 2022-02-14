Домашная работа "Компьютерные сети - 2"

1.
Для Linux:
vagrant@vagrant:~$ ip -c -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128
eth0             UP             10.0.2.15/24 fe80::a00:27ff:feb1:285d/64
Для Windows:
ipconfig

2.
Как было рассказано на вебинаре  для обмена информацией между соседними девайсами существует протокол LLDP. По умолчанию в Ubunto не установлен пакет LLDPD, для этого необходимо выполнить несколько команд:
apt install -y snmpd lldpd
systemctl enable lldpd
systemctl start lldpd
Основная команда у данного пакеты это lldpctl

3.
На L2 коммутаторах для разделения на несколько виртуальных сетей служит технология VLAN. В Linux есть одноименный пакет vlan, который необходимо установить дополнительно.
Чтобы сконфигурировать vlan, необходимо создать новый файл в директории /etc/network/interfaces и добавить следующую конфигурацию для примера:
auto eth0.1400
iface eth0.1400 inet static
        address 192.168.1.1
        netmask 255.255.255.0
        vlan_raw_device eth0
Где
auto eth0.1400 - запускать данные интерфейс автоматом;
iface eth0.1400 - наименование интерфейса;
vlan_raw_device eth0  - привязка к физическому интерфейсу.

4.
В Linux можно объединить несколько канал(интерфейсов) в один логический. При настройке возможно указать тип агрегации, в зависимости от этого будет изменяться нагрузка на интерфейсы.
Типы агрегации:
Mode-0(balance-rr) В данном режиме пакеты отправляются по кругу, сначала на первый интерфейс и тд.
Mode-1(active-backup) Есть 1 активный интерфейс, остальные в в ожидающем режиме. Возникли неполадки с активным - переключить на следующий.
Mode-2(balance-xor) Передача пакетов распределяется по типу входящего и исходящего трафика по формуле ((MAC src) XOR (MAC dest)) % число интерфейсов.
Mode-3(broadcast) Передача пакетов происходит сразу на все интерфейсы.
Mode-4(802.3ad) Динамическое объединение одинаковых портов, позволяет увеличить пропускную способность трафика.
Mode-5(balance-tlb) В данном режиме входящие пакеты принимаются активным интерфейсом, исходящие распределяются между всеми в зависимости от степени загрузки каналов.
Mode-6(balance-alb) Данный вариант похож на предыдущий, но имеет одну особенность - входящие пакеты тоже распределяются между интерфейсами.

5.
В подсети /29 максимальное чисто хостов - 6.
В одной сети с маской /24 можно организовать 32 подсеть с маской /29.
Подсети /29:
10.10.10.0/29
10.10.10.8/29

6.
Я так понимаю, можно воспользоваться сетью 100.64.0.0 — 100.127.255.255 (маска подсети: 255.192.0.0 или /10). И выделить из нее подсеть на 40-50 хостов.
vagrant@vagrant:~$ ipcalc -s 50 100.64.0.0/10
Address:   100.64.0.0           01100100.01 000000.00000000.00000000
Netmask:   255.192.0.0 = 10     11111111.11 000000.00000000.00000000
Wildcard:  0.63.255.255         00000000.00 111111.11111111.11111111
=>
Network:   100.64.0.0/10        01100100.01 000000.00000000.00000000
HostMin:   100.64.0.1           01100100.01 000000.00000000.00000001
HostMax:   100.127.255.254      01100100.01 111111.11111111.11111110
Broadcast: 100.127.255.255      01100100.01 111111.11111111.11111111
Hosts/Net: 4194302               Class A

1. Requested size: 50 hosts
Netmask:   255.255.255.192 = 26 11111111.11111111.11111111.11 000000
Network:   100.64.0.0/26        01100100.01000000.00000000.00 000000
HostMin:   100.64.0.1           01100100.01000000.00000000.00 000001
HostMax:   100.64.0.62          01100100.01000000.00000000.00 111110
Broadcast: 100.64.0.63          01100100.01000000.00000000.00 111111
Hosts/Net: 62                    Class A

Needed size:  64 addresses.
Used network: 100.64.0.0/26

7.
В Windows/Linux ARP таблицу можно посмотреть путем выполнения команды “arp -a”.  Если необходимо удалить один адрес, то необходимо выполнить команду “arp -d 192.168.0.2”.
Очистить ARP-кэш в Windows можно командой “netsh interface ip delete arpcache“, в Linux - “sudo ip neigh flush all”.

