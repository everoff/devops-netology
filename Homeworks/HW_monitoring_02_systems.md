# Домашная работа к занятию "10.02. Системы мониторинга"

[Домашнее задание](https://github.com/netology-code/mnt-homeworks/tree/MNT-13/10-monitoring-02-systems)

## Обязательная часть
1.

2.
- Prometheus - pull
- TICK - push
- Zabbix - push + pull
- VictoriaMetrics - push + pull
- Nagios - pull

3.
Вывод `curl http://localhost:8086/ping -v`
 ```
maksim@MacBook-Pro sandbox % curl http://localhost:8086/ping -v
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8086 (#0)
> GET /ping HTTP/1.1
> Host: localhost:8086
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 204 No Content
< Content-Type: application/json
< Request-Id: bcd2f2bd-3bd8-11ed-8139-0242ac120003
< X-Influxdb-Build: OSS
< X-Influxdb-Version: 1.8.10
< X-Request-Id: bcd2f2bd-3bd8-11ed-8139-0242ac120003
< Date: Sat, 24 Sep 2022 07:15:58 GMT
<
* Connection #0 to host localhost left intact
* Closing connection 0
```

Вывод `curl http://localhost:8888`
```
maksim@MacBook-Pro sandbox % curl http://localhost:8888
<!DOCTYPE html><html><head><link rel="stylesheet" href="/index.c708214f.css"><meta http-equiv="Content-type" content="text/html; charset=utf-8"><title>Chronograf</title><link rel="icon shortcut" href="/favicon.70d63073.ico"></head><body> <div id="react-root" data-basepath=""></div> <script type="module" src="/index.e81b88ee.js"></script><script src="/index.a6955a67.js" nomodule="" defer></script> </body></html>%
```

Вывод `curl http://localhost:9092/kapacitor/v1/ping`
```

```

4.
Скриншот_2


