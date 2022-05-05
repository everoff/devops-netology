## Задача 1 
- При использовании режима global, заданный сервис установится на все ноды кластера, если используется режим replicated - только на указанных в конфиге нодах.
- Для определния лидера в Docker Swarm используется алгоритм Raft. При выходе из строя лидера или при первом запуске кластера, каждый кандидат отправляет запросы на голосованием другим узлам. Лидером становится тот, кто быстрее получит ответы на ранее отправленный запрос, т.е. голоса. В случае, когда кандидаты получают сообщение от лидера, они снимают свои кандидатуры.
- Overlay Network - сли так можно выразиться, то это виртуальная сеть для обмена информацией между контейнерами, присутствует опиция по шифрованию данных.


---

## Задача 2

![Task2](https://github.com/everoff/devops-netology/blob/main/Homeworks/HW_script_05_docker-swarm/task_2.png)


---

## Задача 3

![Task3](https://github.com/everoff/devops-netology/blob/main/Homeworks/HW_script_05_docker-swarm/task_3.png)

---

## Задача 4*

Данная команда включает функцию шифрования логов и данных, необходимых для обмена между нодами. Это позволяет повысить безопасность. Ключ шифрования хранится в памяти всех менеджеров.

```
[root@node01 ~]# docker swarm update --autolock=true
Swarm updated.
To unlock a swarm manager after it restarts, run the `docker swarm unlock`
command and provide the following key:

    SWMKEY-1-rZF9Fo/b8myMJYrLJ7QfvjhClk4qiWeAIojnnzBkW20

Please remember to store this key in a password manager, since without it you
will not be able to restart the manager.
```
```
[root@node01 ~]# service docker restart
Redirecting to /bin/systemctl restart docker.service
[root@node01 ~]# docker node ls
Error response from daemon: Swarm is encrypted and needs to be unlocked before it can be used. Please use "docker swarm unlock" to unlock it.
```



