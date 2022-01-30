# devops-netology
Домашная работа "Операционные системы (лекция 1)"

1.
Выполняем команду  
vagrant@vagrant:~$ strace /bin/bash -c 'cd /tmp' 2>&1 | grep /tmp
execve("/bin/bash", ["/bin/bash", "-c", "cd /tmp"], 0x7ffca1d69ca0 /* 26 vars */) = 0
stat("/tmp", {st_mode=S_IFDIR|S_ISVTX|0777, st_size=4096, ...}) = 0
chdir("/tmp")
Ответ: chdir("/tmp") 

2.
Файл базы типов находится в следующей дирректории "/usr/share/misc/magic.mgc”, это можно наблюдать выводе команды strace file.
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3

3. 
vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
   2580 pts/1    00:00:00 vim
   2581 pts/0    00:00:00 ps
vagrant@vagrant:~$ sudo lsof -p 2580 | grep task3.2.txt
vim     2580 vagrant    4u   REG  253,0    12288 1048620 /home/vagrant/homework/.task3.2.txt.swp
vagrant@vagrant:~$ rm /home/vagrant/homework/.task3.2.txt.swp
vagrant@vagrant:~$ cat /home/vagrant/homework/.task3.2.txt.swp
cat: /home/vagrant/homework/.task3.2.txt.swp: No such file or directory
vagrant@vagrant:~$ sudo lsof -p 2580 | grep task3.2.txt
vim     2580 vagrant    4u   REG  253,0    12288 1048620 /home/vagrant/homework/.task3.2.txt.swp (deleted)
vagrant@vagrant:~$ echo '' >/proc/2580/fd/4

vagrant@vagrant:~$ cat /proc/2580/fd/4  > /home/vagrant/homework/.task3.2.txt.swp
vagrant@vagrant:~$ cat /home/vagrant/homework/.task3.2.txt.swp

4. 
Процессы со статусом “зомби” не занимают памяти (как “сироты”), но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом.  Запись в таблице освободится, при вызову родительским процессом системного вызова wait().

5. 
vagrant@vagrant:~$ sudo -i
root@vagrant:~# /usr/sbin/opensnoop-bpfcc
PID    COMM               FD ERR PATH
906    vminfo              4   0 /var/run/utmp
641    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
641    dbus-daemon        21   0 /usr/share/dbus-1/system-services
641    dbus-daemon        -1   2 /lib/dbus-1/system-services
641    dbus-daemon        21   0 /var/lib/snapd/dbus-1/system-services/

6. 
Выполняется системный вызов uname ()
Выдержка из man: 
Part of the utsname information is also accessible via
       /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.

Например:
vagrant@vagrant:~$ cat /proc/sys/kernel/osrelease
5.4.0-91-generic

7.
Символ ; - служит разделителем последовательных команд
Символ && - логический оператор “И”, те в данной случае вторая команда будет выполняться только после того, как первая выполнилась удачно(вернулся 0)

Команда Set -e текущую прерывает сессию если команда завершилась с ненулевым статусом.
Думаю, нет смысла использовать && вместе с set -e, тк при возникновении ошибки выполнении команд, дальнейшее выполнение остановится

8.
-e прерывает сессию если команда завершилась с ненулевым статусом.
-u неустановленные/не заданные параметры и переменные считаются как ошибки
-x вывод перечень команд с аргументами, если они выполняются.
-o pipefail позволяет вывести статус выполнения набора команд, 0 - если все выполнено успешно, ненулевой - для последней команды.

Эти опции полезны для отладки сценариев(дополнительное логирование)
 
9.
Наиболее часто встречающийся статус у процессов в системе согласно выводу команды ps -o stat:
S(Ssl, Sl, S<) - процессы, ожидающие завершения;
I(I, I<) - фоновые процессы ядра.
