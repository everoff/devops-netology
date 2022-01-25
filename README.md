# devops-netology
Домашная работа "Работа в терминале Лекция 2"

1. cd имеет тип “встроенная в оболочку”, воспользовался командой type cd. Теоретически можно сделать cd внешней.

2. В данном случае stdout для “grep <some_string> <some_file>” передается в stdin “wc -l”
Альтернативный вариант grep -с <some_string> <some_file>

3. Процесс systemd имеет PID 1

4. Выполним следующую команду ls -l /prot/$$/fd 2>/dev/pts/1
После чего stderr будет перенаправлен в терминал /dev/pts/1

5. cat readme.txt>task5.txt

6. Вывести можно, если перенапрвить stdout из pty  в tty, но чтобы наблюдать вывод необходимо переключиться на данный tty.

7. Команда bash 5>&1 создает новый дескриптер и перенаправляет его на stdout. При выполнении команды echo netology > /proc/$$/fd/5 в терминале выводится netology. Stdout перенаправляется в ранее созданный файл дескриптера 5.

8.
vagrant@vagrant:~$ touch task8.txt
vagrant@vagrant:~$ echo string for task > task8.txt
vagrant@vagrant:~$ cat task8.txt | wc -w
3
vagrant@vagrant:~$ cat task8.txt 3>&1 1>&2 2>&3 | wc -w
string for task
0

9. Команда cat /proc/$$/environ выдает начальную окружающую среду(различные переменные окружения), которая была задана при первом запуске. Подобную информацию можно получить путем выполнения команды printenv.

10. 
proc/<PID>/cmdline содержит все аргументы, которые были переданы ядру Linux при запуске
/proc/<PID>/exe файл представляет из себя ссылку, содержащую путь к выполняемой команде

11. Версия набора инструментов SSE процессора sse4_2

12. Это происходит по следующей причине: изначальной TTY не выделяется при подключении. Если нужно включить оболочку,  можно выполнить команду ssh user@vagrant -t "ssh otheruser@vagrant”.

13. Выполнено.
В первом терминале выполняем следующие команды:
vagrant@vagrant:~$ screen
vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
   1341 pts/0    00:00:00 screen
   1350 pts/1    00:00:00 ps
vagrant@vagrant:~$ tmux
vagrant@vagrant:~$ sudo reptyr -T 1341
В новом терминале выполняем:
vagrant@vagrant:~$ tmux attach

14. Команда tee осуществляет перенаправление потока с stdin на stdout плюс запись в файлы. Sudo tee получает вывод команды echo повышает привелегии пользователя и записывает в файл.
