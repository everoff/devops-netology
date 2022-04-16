#Домашняя работа на тему "Применение принципов IaaC в работе с виртуальными машинами"

1.
IaaC - это метод, позволяющий автоматизировать процесс разворачивания инфраструктуру(например, тестовых стендов) с помощью кода. В свою очередь, применение этого метода требует знаний основ программирования, но это стоит того. А если код написан по всем правилам, в том числе с комментариями, то получаем своего роде документацию. В итоге проще разобраться, как построен процесс.
Преимуществами является:
Скорость и масштабируемость. Применяя IaaC, можно достаточно быстро конфигурировать необходимую инфраструктуру, экономя при этом ресурсы компании. А их уже можно будет направить на выполнение более важных задач, нежели ручная настройка конфигураций.
Стандартизация. Исключает различие конфигураций, в том числе различные опечатки, на серверах. Т.е. сведение к минимуму человеческого фактора.
Основополагающим принципом при применении IaaC является идемпотентность, т.е. результат выполнения того или иного действия будет аналогичен предыдущему выполнению. Если так можно выразиться - предсказуемый результат.


2.
Считаю, что push метод надежнее. Во-первых, при pull необходимо ставить специальный клиент на машины, что является дополнительным элементом в системе. А чем меньше элементов и разных прослоек, тем надежнее она. Как раз одной из особенностей Ansible является использование push-метода доставки конфигураций. Также для Ansible не требуется инфраструктура открытого ключа(PKI), чем упрощается настройка и использование Ansible.

3.
maksim@MacBook-Pro ~ % vboxmanage --version
6.1.28r147628

maksim@MacBook-Pro ~ % vagrant -v
Vagrant 2.2.19

maksim@MacBook-Pro ~ % ansible --version
ansible [core 2.12.4]
  config file = None
  configured module search path = ['/Users/maksim/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /Library/Python/3.8/site-packages/ansible
  ansible collection location = /Users/maksim/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.8.9 (default, Oct 26 2021, 07:25:54) [Clang 13.0.0 (clang-1300.0.29.30)]
  jinja version = 3.1.1
  libyaml = True


4.

maksim@MacBook-Pro vagrant % vagrant ssh
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri 15 Apr 2022 05:42:52 PM UTC
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

