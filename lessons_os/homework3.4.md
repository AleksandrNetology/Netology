### Домашнее задание к занятию "3.4. Операционные системы, лекция 2"


1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background.
Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по `systemd`,
создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для `node_exporter`:

- поместите его в автозагрузку,
- предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
- удостоверьтесь, что с помощью `systemctl` процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.

----
[Пример](https://itdraft.ru/2020/11/02/ustanovka-node-exporter-s-avtorizacziej-i-podklyuchenie-k-prometheus-v-centos-8/)
Скачиваем node_exporter, причём нам нужна версия node_exporter-1.3.1.__linux__-amd64, иначе эта хрень не заработает:
```sh
vagrant@vagrant:~/tmp$ wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
```
Распаковываем:
```sh
vagrant@vagrant:~/tmp$ tar -zxpvf node_exporter-1.3.1.linux-amd64
```
Копируем исполняемый файл в `/usr/local/bin`, иначе надо прописывать путь в переменной $PATH:
```sh
vagrant@vagrant:~$ sudo cp tmp/node_exporter-1.3.1.linux-amd64/node_exporter /usr/local/bin
```
Создаем Systemd Unit:
```sh
vagrant@vagrant:~$ sudo nano /etc/systemd/system/node_exporter.service

	[Unit]
	Description=Node Exporter
	
	[Service]
	ExecStart=/usr/local/bin/node_exporter
	EnvironmentFile=/etc/default/node_exporter
	
	[Install]
	WantedBy=multi-user.target
```
Добавляем сервис в автозагрузку, запускаем его, проверяем статус:
```sh
vagrant@vagrant:~$ sudo systemctl daemon-reload				# обновляем конфигурацию всех служб
vagrant@vagrant:~$ sudo systemctl enable --now node_exporter		# добавляем node_exporter в автозагрузку
Created symlink /etc/systemd/system/multi-user.target.wants/node_exporter.service → /etc/systemd/system/node_exporter.service.

vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-03-07 15:50:19 UTC; 8s ago
   Main PID: 17336 (node_exporter)
      Tasks: 4 (limit: 2320)
     Memory: 2.3M
     CGroup: /system.slice/node_exporter.service
             └─17336 /usr/local/bin/node_exporter

Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=thermal_zone
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=time
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=timex
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=udp_queues
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=uname
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=vmstat
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=xfs
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.842Z caller=node_exporter.go:115 level=info collector=zfs
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.843Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
Mar 07 15:50:19 vagrant node_exporter[17336]: ts=2022-03-07T15:50:19.844Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
```
Из лога видим,  что сервис `node_exporter` ожидает подключений на порту 9100.
Проверяем порт 9100:
```sh
vagrant@vagrant:~$ sudo ss -pnltu | grep 9100
tcp     LISTEN   0        4096                    *:9100                *:*      users:(("node_exporter",pid=17336,fd=3))
```
Пробрасываем порт 9100 с ВМ на хост в наcтройках VirtualBox:

![](port9100-9191.jpg)


Подключаемся и проверяем:

![](metrics.jpg)

На самой виртуальной машине это можно сделать с помощью команды:
```sh
vagrant@vagrant:~$ curl -s http://localhost:9100/metrics
```

Удостоверился, что процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.

----

2. Ознакомьтесь с опциями `node_exporter` и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

----
```sh
[CPU]
node_cpu_seconds_total{cpu="1",mode="system"} 9.51
node_cpu_seconds_total{cpu="1",mode="user"} 2.31
node_cpu_seconds_total{cpu="0",mode="idle"} 15921.68

[Memory]
node_memory_MemAvailable_bytes 1.790394368e+09
node_memory_MemFree_bytes 1.530048512e+09

[Network]
node_network_receive_bytes_total{device="eth0"} 54586
node_network_receive_packets_total{device="eth0"} 318
node_network_receive_drop_total{device="eth0"} 0
node_network_receive_errs_total{device="eth0"} 0

node_network_transmit_bytes_total{device="eth0"} 40107
node_network_transmit_packets_total{device="eth0"} 235
node_network_transmit_drop_total{device="eth0"} 0
node_network_transmit_errs_total{device="eth0"} 0

[Disk]
node_disk_io_time_seconds_total{device="sda"} 21.836000000000002
node_disk_read_bytes_total{device="sda"} 2.89396736e+08
node_disk_read_time_seconds_total{device="sda"} 50.045
node_disk_write_time_seconds_total{device="sda"} 13.19
```
----

3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`).\
	После успешной установки:

- в конфигурационном файле `/etc/netdata/netdata.conf` в секции `[web]` замените значение с `localhost` на `bind to = 0.0.0.0`,
- добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:
```sh
config.vm.network "forwarded_port", guest: 19999, host: 19999
```
После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`.
Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.

----

----

4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
----

>Осознание является формой психической активности и элементом высшей нервной деятельности, основой которой является головной мозг.

Таким образом, виртуальная машина __не__ способна что-либо осознавать.\
Более того, на текущий момент человечество не смогло создать полноценного разума, способного себя осознать.\
__По этой причине, вопрос абсолютно не корректен.__

Однако, если __человек__ посмотрим на вывод `dmesg`, то __ему__ станет понятно, что эта ОС выполняется в виртуальной среде.
```sh
vagrant@vagrant:~$ dmesg -T | grep virtual
[Mon Mar  7 19:11:15 2022] CPU MTRRs all blank - virtualized system.
[Mon Mar  7 19:11:15 2022] Booting paravirtualized kernel on KVM
[Mon Mar  7 19:11:29 2022] systemd[1]: Detected virtualization oracle.
```

----

5. Как настроен `sysctl` `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?

----

----

6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов;
	покажите, что ваш процесс работает под `PID 1` через `nsenter`. Для простоты работайте в данном задании под __root__ (`sudo -i`).
	Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.

----

----

7. Найдите информацию о том, что такое `:(){ :|:& };:`.\
	Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (__это важно, поведение в других ОС не проверялось__).\
	Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться.\
	Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации.\
	Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?

----

----


###### Примечания

Про unit-файлы:
- [__systemd: основные приемы работы с юнит-файлами__](http://rus-linux.net/MyLDP/boot/systemd_2_getting_a_grip_on_units.html)
- [__systemd Часть 1: Понимание systemd, типы юнитов и целевые юниты__](https://max-ko.ru/9-systemd-i-junity.html)
- [__systemd Часть 2: Управление юнитами, зависимостями и целями__](https://max-ko.ru/10-systemd-chast-2-upravlenie-junitami-zavisimostjami-i-celjami.html)

[__Установка и настройка Prometheus__](https://losst.ru/ustanovka-i-nastrojka-prometheus)
[__Осваиваем мониторинг с Prometheus. Часть 1. Знакомство и установка__](https://laurvas.ru/prometheus-p1/#node-exporter-installation)

[__Проброс портов в VirtualBox__](https://losst.ru/probros-portov-virtualbox)
[__Настройка сети VirtualBox__](https://help.reg.ru/hc/ru/articles/4408054736529-%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D1%81%D0%B5%D1%82%D0%B8-VirtualBox)



