Домашная работа "Компьютерные сети - 3"

1.
Подключился к публичному маршрутизатору.
route-views>show bgp 89.109.51.94
BGP routing table entry for 89.109.51.0/24, version 273737342
Paths: (3 available, best #3, table default)
  Not advertised to any peer
  Refresh Epoch 1
  701 1273 12389
    137.39.3.55 from 137.39.3.55 (137.39.3.55)
      Origin IGP, localpref 100, valid, external
      path 7FE1105AC098 RPKI State invalid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20912 49367 6762 12389
    212.66.96.126 from 212.66.96.126 (212.66.96.126)
      Origin IGP, localpref 100, valid, external
      Community: 6762:1 6762:30 6762:40 6762:14900 20912:65005 49367:2 49367:6762
      path 7FE120B7E128 RPKI State invalid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  49788 12552 12389
    91.218.184.60 from 91.218.184.60 (91.218.184.60)
      Origin IGP, localpref 100, valid, external, best
      Community: 12552:12000 12552:12100 12552:12101 12552:22000
      Extended Community: 0x43:100:1
      path 7FE1559C0CB8 RPKI State invalid
      rx pathid: 0, tx pathid: 0x0
route-views>show ip route 89.109.51.94
Routing entry for 89.109.51.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 49788, type external
  Last update from 91.218.184.60 3w1d ago
  Routing Descriptor Blocks:
  * 91.218.184.60, from 91.218.184.60, 3w1d ago
      Route metric is 0, traffic share count is 1
      AS Hops 3
      Route tag 49788
      MPLS label: none

2.
Создал виртуальный интерфейс dummy0 с адресом 192.168.2.2/32.
root@vagrant:~# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
3: dummy0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether f6:fa:c6:de:0a:70 brd ff:ff:ff:ff:ff:ff
Добавил несколько статических маршрутов:
root@vagrant:~# ip route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
192.168.2.64/26 via 10.0.2.2 dev eth0
192.168.5.7 via 10.0.2.2 dev eth0

3, 4
vagrant@vagrant:~$ ss -tulpn
Netid     State      Recv-Q      Send-Q            Local Address:Port           Peer Address:Port     Process
udp       UNCONN     0           0                 127.0.0.53%lo:53                  0.0.0.0:*
udp       UNCONN     0           0                10.0.2.15%eth0:68                  0.0.0.0:*
udp       UNCONN     0           0                     127.0.0.1:161                 0.0.0.0:*
udp       UNCONN     0           0                         [::1]:161                    [::]:*
tcp       LISTEN     0           4096              127.0.0.53%lo:53                  0.0.0.0:*
tcp       LISTEN     0           128                     0.0.0.0:22                  0.0.0.0:*
tcp       LISTEN     0           128                        [::]:22                     [::]:*

Вывод команды показал, что открыты порты TCP: 22 и 53. 22 использует SSH-сервер, это его порт по умолчанию. 
DNS использует UDP порт 53, но TCP порт 53 также зарезервирован для DNS. 
Для SNMP службы зарезервирован 161 UDP порт.


