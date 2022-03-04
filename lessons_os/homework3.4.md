### Домашнее задание к занятию "3.4. Операционные системы, лекция 2"


1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background.
Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по `systemd`,
создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для `node_exporter`:

- поместите его в автозагрузку,
- предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
- удостоверьтесь, что с помощью `systemctl` процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.

----

Скачиваем node_exporter:
```sh
vagrant@vagrant:~/tmp$ wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.darwin-amd64.tar.gz
```
Распаковываем:
```sh
vagrant@vagrant:~/tmp$ tar -zxpvf node_exporter-1.3.1.darwin-amd64.tar.gz
```
Копируем исполняемый файл в `/usr/local/bin`, иначе надо прописывать путь в переменной $PATH:
```sh
vagrant@vagrant:~$ sudo cp tmp/node_exporter-1.3.1.darwin-amd64/node_exporter /usr/local/bin
```
Создаем Systemd Unit:
```sh
vagrant@vagrant:~$ sudo nano /etc/systemd/system/node_exporter.service

	[Unit]
	Description=Prometheus Node Exporter
	Wants=network-online.target
	After=network-online.target

	[Service]
	User=node_exporter
	Group=node_exporter
	Type=simple
	ExecStart=/usr/local/bin/node_exporter

	[Install]
	WantedBy=multi-user.target
```
Добавляем сервис в автозагрузку, запускаем его, проверяем статус:
```sh
vagrant@vagrant:~$ sudo systemctl daemon-reload
vagrant@vagrant:~$ sudo systemctl enable --now node_exporter
Created symlink /etc/systemd/system/multi-user.target.wants/node_exporter.service → /etc/systemd/system/node_exporter.service.
vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Fri 2022-03-04 09:04:41 UTC; 11s ago
    Process: 4884 ExecStart=/usr/local/bin/node_exporter (code=exited, status=217/USER)
   Main PID: 4884 (code=exited, status=217/USER)

Mar 04 09:04:41 vagrant systemd[1]: Started Node Exporter.
Mar 04 09:04:41 vagrant systemd[1]: node_exporter.service: Main process exited, code=exited, status=217/USER
Mar 04 09:04:41 vagrant systemd[1]: node_exporter.service: Failed with result 'exit-code'.
```



















----

2. Ознакомьтесь с опциями `node_exporter` и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

----

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


