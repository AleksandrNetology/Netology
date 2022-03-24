# Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

__1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?__

=Выполнение=
----
Ubuntu:
```sh
vagrant@vagrant:~$ ip -c -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
eth0             UP             08:00:27:b1:28:5d <BROADCAST,MULTICAST,UP,LOWER_UP>
vagrant@vagrant:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
```
Ещё можно использовать `ifconfig`:
```sh
vagrant@vagrant:~$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::a00:27ff:feb1:285d  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:b1:28:5d  txqueuelen 1000  (Ethernet)
        RX packets 184586  bytes 211297002 (211.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 67273  bytes 5394728 (5.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 982  bytes 90124 (90.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 982  bytes 90124 (90.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Windows: `ipconfig /all`

=Выполнено=
----

__2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?__

=Выполнение=
----

Протокол канального уровня `LLDP` (__Link Layer Discovery Protocol__) предназначен для обмена информацией между соседними устройствами.
Он позволяет сетевому оборудованию оповещать соседей о своём существовании, передавать им свои характеристики, а также получать от них аналогичную инфу. Требуется настройка.

`sudo apt update` - иначе не заработает\
`sudo apt-get install lldpd`

```sh
vagrant@vagrant:~$ sudo systemctl enable lldpd
Synchronizing state of lldpd.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable lldpd
vagrant@vagrant:~$ sudo systemctl start lldpd
vagrant@vagrant:~$ lldpctl
-------------------------------------------------------------------------------
LLDP neighbors:
...
```

=Выполнено=
----

__3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.__

=Выполнение=
----
Устанавливаем пакет: `sudo apt-get install -y vlan`

Пример:
```sh
nano /etc/network/interfaces

auto vlan333
iface vlan333 inet static
        address 192.168.0.1
        netmask 255.255.255.0
        vlan_raw_device eth0

auto eth0.333
iface eth0.333 inet static
        address 192.168.0.1
        netmask 255.255.255.0
        vlan_raw_device eth0
```

=Выполнено=
----

__4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.__

=Выполнение=
----

Описание режимов работы бондинга нашёл [__тут__](https://itproffi.ru/obedinenie-setevyh-interfejsov-v-linux-nastrojka-bonding/):
- mode = 0 (round robin) - пакеты по очереди направляются на сетевые карты объединённого интерфейса;
- mode = 1 (active-backup) - активен только один физический интерфейс, а остальные работают как резервные;
- mode = 2 (balance-xor) - объединенный интерфейс определяет, через какую физическую сетевую карту отправить пакеты, в зависимости от MAC-адресов источника и получателя;
- mode = 3 (broadcast)- Широковещательный режим, все пакеты отправляются через каждый интерфейс;
- mode = 4 (802.3ad) - Особый режим объединения. Для него требуется специально настраивать коммутатор, к которому подключен объединенный интерфейс;
- mode = 5 (balance-tlb) - Распределение нагрузки при передаче;
- mode = 6 (balance-alb) - Адаптивное распределение нагрузки.

Пример файла конфигурации:
```sh
nano /etc/network/interfaces

auto bond0

iface bond0 inet static
    address 192.168.1.5
    netmask 255.255.255.0
    network 192.168.1.0
    gateway 192.168.1.1
    bond-slaves eth0 eth1
    bond-mode active-backup
    bond-miimon 100
    bond-downdelay 200
    bond-updelay 200
```

=Выполнено=
----

__5. Сколько IP адресов в сети с маской /29?\
Сколько /29 подсетей можно получить из сети с маской /24?\
Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.__

=Выполнение=
----
- Сколько IP адресов в сети с маской /29?\
	`Всего адресов 8, но только 6 IP-адресов можно использовать для хостов`;
	
- Сколько /29 подсетей можно получить из сети с маской /24? Привести примеры.
	
Всего может быть 32 сети:

	Сеть:	10.10.10.0/29
	Минимальный IP:	10.10.10.1
	Максимальный IP:	10.10.10.6
	Broadcast:		10.10.10.7
	Число хостов:	6

	Сеть:	10.10.10.8/29
	Минимальный IP:	10.10.10.9
	Максимальный IP:	10.10.10.14
	Broadcast:		10.10.10.15
	Число хостов:	6

	Сеть:	10.10.10.16/29
	Минимальный IP:	10.10.10.17
	Максимальный IP:	10.10.10.22
	Broadcast:		10.10.10.23
	Число хостов:	6
	
	Сеть:	10.10.10.24/29
	Минимальный IP:	10.10.10.25
	Максимальный IP:	10.10.10.30
	Broadcast:		10.10.10.31
	Число хостов:	6
	
	Сеть:	10.10.10.0/29
	Минимальный IP:	10.10.10.1
	Максимальный IP:	10.10.10.6
	Broadcast:		10.10.10.7
	Число хостов:	6
__и т.д.__

=Выполнено=
----

__6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты.
Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.__

=Выполнение=
----

Думаю, что смело можно брать адреса из частного диапазона 100.64.0.0 — 100.127.255.255 с маской подсети 255.192.0.0 или /10.

Выбираем подсеть, например, 100.64.255.0/10 из всего диапазона.\
Далее, в условии сказано, что адресов надо 40-50, но мы можем нарезать подсеть либо на 32 адреса, либо на 64. Очевидно, выбираем 64 адреса.

Таким образом у нас получится что-то такое:
```sh
vagrant@vagrant:~$ ipcalc -b --split 50 100.64.255.0/10
Address:   100.64.255.0
Netmask:   255.192.0.0 = 10
Wildcard:  0.63.255.255
=>
Network:   100.64.0.0/10
HostMin:   100.64.0.1
HostMax:   100.127.255.254
Broadcast: 100.127.255.255
Hosts/Net: 4194302               Class A

1. Requested size: 50 hosts
Netmask:   255.255.255.192 = 26
Network:   100.64.0.0/26
HostMin:   100.64.0.1
HostMax:   100.64.0.62
Broadcast: 100.64.0.63
Hosts/Net: 62                    Class A

Needed size:  64 addresses.
Used network: 100.64.0.0/26
Unused:
100.64.0.64/26
100.64.0.128/25
100.64.1.0/24
100.64.2.0/23
100.64.4.0/22
100.64.8.0/21
100.64.16.0/20
100.64.32.0/19
100.64.64.0/18
100.64.128.0/17
100.65.0.0/16
100.66.0.0/15
100.68.0.0/14
100.72.0.0/13
100.80.0.0/12
100.96.0.0/11
```

__Ответ:__
```sh
Netmask:   255.255.255.192 = 26
Network:   100.64.0.0/26
HostMin:   100.64.0.1
HostMax:   100.64.0.62
Broadcast: 100.64.0.63
Hosts/Net: 62
```

=Выполнено=
----

__7. Как проверить ARP-таблицу в Linux, Windows? Как очистить ARP-кеш полностью? Как из ARP-таблицы удалить только один нужный IP?__

=Выполнение=
----

Посмотреть ARP-таблицу:
- в Windows: `arp -a`;
- в Linux: `arp -e`.

Очистить ARP-кеш полностью:
- в Windows: `netsh interface ip delete arpcache`;
- в Linux: `sudo ip -s -s neigh flush all`.

Удаление конкретной записи:
- в Windows: `arp -d <ip-address>`;
- в Linux: `arp -v -i eth0 -d <ip-address>`.

=Выполнено=
----
