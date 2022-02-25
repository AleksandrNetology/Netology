## Операционные системы. Лекция 2.

#### Оценка потребления ресурсов

Хорошая статья на тему оценки производительности __[здесь](https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55).__

Краткая выдержка из неё по командам:

`uptime` — текущий аптайм, `Load Average` за 1, 5 и 15 минут соответственно;

`dmesg` — логи ядра, здесь могут быть ошибки, которые говорят о проблемах производительности (`oomkiller`, деградация дисковой подсистемы, сети и др.)\
Лучше использовать с ключом `-Т` для адекватного отображения времени: `dmesg -T`. Сравнительные примеры ниже.

	vagrant@vagrant:~$ dmesg
	[   46.052590] 13:14:10.463798 main     Executable: /opt/VBoxGuestAdditions-6.1.30/sbin/VBoxService
               13:14:10.463799 main     Process ID: 831
               13:14:10.463799 main     Package type: LINUX_64BITS_GENERIC
	[   46.058178] 13:14:10.469384 main     6.1.30 r148432 started. Verbose level = 0
	[   46.068606] 13:14:10.479786 main     vbglR3GuestCtrlDetectPeekGetCancelSupport: Supported (#1)

Тоже самое но в адекватном виде:

	vagrant@vagrant:~$ dmesg -T
	[Tue Feb 15 13:14:10 2022] 13:14:10.463798 main     Executable: /opt/VBoxGuestAdditions-6.1.30/sbin/VBoxService
                           13:14:10.463799 main     Process ID: 831
                           13:14:10.463799 main     Package type: LINUX_64BITS_GENERIC
	[Tue Feb 15 13:14:10 2022] 13:14:10.469384 main     6.1.30 r148432 started. Verbose level = 0
	[Tue Feb 15 13:14:10 2022] 13:14:10.479786 main     vbglR3GuestCtrlDetectPeekGetCancelSupport: Supported (#1)

----
Для дальнейшей работы нам потребуется большое количество разнообразных диагностических утилит.\
Все они содержатся в двух пакетах, которые необходимо будет установить, если их нет.\
Установка: `sudo apk-get install sysstat`\
Установка: `sudo apk-get install procps` - этот пакет, как правило, включён в дистрибутивы ОС.

Утилита `vmstat`, пакет `procps`.

`vmstat <interval>` — общие метрики производительности в одном месте, выводятся на терминал раз в интервал времени <interval> в секундах:
- r, b — количество процессов в состоянии R и D соответственно;
- swpd, free, buff, cache — потребление памяти в системе: файл подкачки, свободной, выделенной под буферы и кеш;
- si, so - количество операций ввода/вывода swap;
- bi, bo — количество блоков прочитанных и записанных на диск;
- in, cs - количество прерываний и количество переключенией контекста - это когда планировщик ОС переключает другой процесс на это же ядро, отключив прежний процесс;
- us, sy, id, wa — процент времени, когда CPU был утилизирован под `userspace`; `kernelspace` задачи; когда он не выполнял никакой работы; когда задачи ожидали ввода/вывода.
```sh
	vagrant@vagrant:~$ vmstat 1
	procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
	r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
	1  0      0 1525840  42260 339852    0    0    66     6   19   79  0  0 100  0  0
	0  0      0 1525832  42260 339852    0    0     0     0   32   52  0  0 100  0  0
	0  0      0 1525832  42260 339852    0    0     0     0   31   53  0  0 100  0  0
	0  0      0 1525832  42260 339852    0    0     0     0   35   49  0  0 100  0  0
```
Причём первая строчка вывода - это среднее значение меток за всё время работы системы - не очень нужные данные.
Если выводить данные каждую секунду, то можно отловить какие-то пики в работе системы т.е. неравномерную нагрузку.

Тоже самое, но память в мегабайтах:

	vagrant@vagrant:~$ vmstat -S M 1
	procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
	r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
	1  0      0   1490     41    331    0    0    54     5   18   68  0  0 100  0  0
	0  0      0   1490     41    331    0    0     0     0   26   36  0  0 100  0  0
	0  0      0   1490     41    331    0    0     0     0   27   41  0  0 100  0  0

----

Утилита `mpstat` ставится с пакетом `sysstat`.

`mpstat` — утилизация процессора в разрезе ядер, с помощью которой можно увидеть дисбаланс распределения нагрузки по ядрам;

В примере ниже, мы просим выводить загрузку по всем CPU каждые 2 секунды:

	vagrant@vagrant:~$ mpstat -P ALL 2
	Linux 5.4.0-91-generic (vagrant)        02/16/2022      _x86_64_        (2 CPU)
	
	02:53:34 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
	02:53:36 PM  all    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
	02:53:36 PM    0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
	02:53:36 PM    1    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00

----
Утилита `pidstat` также содержится в пакете `sysstat`.

`pidstat` — раз в интервал выводит полезные метрики утилизации ресурсов по процессам (CPU, RAM, IO);

Основные ключи утилиты:
- -t показывает треды;
- -d показывает метрики ввода/вывода;
- -r утилизация памяти;
- -u утилизация процессора.

Пример работы:

	vagrant@vagrant:~$ pidstat
	Linux 5.4.0-91-generic (vagrant)        02/16/2022      _x86_64_        (2 CPU)

	02:57:27 PM   UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
	02:57:27 PM     0         1    0.00    0.01    0.00    0.01    0.01     1  systemd
	02:57:27 PM     0         2    0.00    0.00    0.00    0.00    0.00     1  kthreadd
	02:57:27 PM     0         9    0.00    0.00    0.00    0.00    0.00     0  ksoftirqd/0
	02:57:27 PM     0        10    0.00    0.01    0.00    0.01    0.01     1  rcu_sched
	02:57:27 PM     0       523    0.03    0.03    0.00    0.00    0.05     0  multipathd

%usr - утилизация CPU в `userspace`;\
%system - утилизация CPU в `kernelspace`. Здесь значение больше, если процесс занят вводом\выводом, т.е. чаще работаю системные вызовы;

Как обычно, больше информации в `man` или __[посмотреть тут](https://bloglinux.ru/1972-utilita-pidstat-v-unix-linux.html).__

----
Утилита `iostat`, находится в пакете `sysstat`.

`iostat` — метрики подсистемы ввода вывода дисковых устройств:
- r/s, w/s количество запросов чтения и записи к устройству;
- rkB/s, wkB/s количество байт считанных или записанных;
- avgrq-sz, avgqu-sz средний размер запроса в байтах и среднее количество запросов в очереди на обслуживание;
- r_await, w_await среднее время обслуживание запроса в миллисекундах, включая ожидание в очереди.

----

`free` — использование памяти в системе, пакет `procps`.

	vagrant@vagrant:~$ free -m
			total        used        free      shared  buff/cache   available
	Mem:           2992         136        2446           0         409        2697
	Swap:          1961           0        1961

- total - количество памяти в истеме;
- user - количество памяти занятое программами;
- free - свободное количество памяти;
- shared - ;
- buff/cache - память ядра, выделенная под буфер устройств ввода/вывода и page-кэш, в котором хранятся уже прочитанные файлы;
- available - всего доступной памяти.

Ядро старается максимально оптимизировать расход памяти, поэтому всю память из free она отправляет на хренение 
буфера и кэша, но если вдруг память потребуется какому-то процессу - то она берётся именно из раздела buff/cache.

__total = used + free + buff/cache__\
Помня, что раздел buff/cache может быть _почти_ полностью освобождён, можно считать, что свободной памяти у нас __free + buff/cache__.

available - это free + buff/cache минус то, что невозможно освободить в разделе free + buff/cache.

----

Ещё одна популярная утилита для мониторинга производительности системы - __sar__.

Эта утилита не входит в стандартную поставку, она содержится в пакете `sysstat`.\
Помимо её установки, требуется настройка, а именно редактирование файлов конфигурации:\
`sudo nano /etc/default/sysstat` - в этом файле меняем строку с `ENABLED=”false”` на `ENABLED=”true”`, сохраняем.\
Теперь изменим интервал сбора информации с каждых 10 минут на каждые 2 минуты:\
`sudo vim /etc/cron.d/sysstat`:

	#Меняем строчку с
	5-55/10 * * * * root command -v debian-sa1 > /dev/null && debian-sa1 1 1
	#На
	*/2 * * * * root command -v debian-sa1 > /dev/null && debian-sa1 1 1

И перезапускаем сервис `sysstat`: `sudo service sysstat restart`

`sar` приступает к сбору информации о работе системы.

Эта утилита может показывать раличные метрики производительности:
- -n DEV, -n EDEV - утилизация сетевых интерфейсов и дропы пакетов на них - колонки `rxdrop` и `txdrop`. Это канальный уровень, фреймы ethernet;
- -n IP 1 - статистика IP-пакетов: irec, fwddgm - количество принятых и маршрутизированных пакетов;
- -n TCP, -n ETCP, -n UDP метрики TCP и UDP протоколов (кол-во соединений в секунды, ретрансмиты и др.);
- -B метрики использования page cache;
- -b, -d метрики IO по устройствам;
- -F метрики от смонтированных файловых систем;

Потери (дропы) пакетов происходят из-за того, что сетевой интерфейс слишком сильно загружен (утилизирован) и поступающие на него очередные пакеты
переполняют буфер, в результате чего ядро ОС вынуждено отбрасывать "лишние" пакеты.

----

Семейство утилит `top`: `top`, `atop`, `htop`

- `top` - выводит список активных процессов системы с сортировкой по потребляемому процессорному времени (по умолчанию).\
	Комбинация `Shift+F` включает интерактивное меню, где `s` - меняет сортировку, а `d` - включает/выключает отображение колонки;

- `atop` - программа записи истории выводка команды `top` с заданным интервалом.
	Комбинация `t/Shift+t` используется для перехода по интервалам,\
	`atop -r <log> -b <time>` - для старта с нужного времени.
	`echo LOGINTERVAL=10 > /etc/default/atop; systemctl restart atop`

- `htop` - более современный и удобный вариант `top`.

- `iotop` - утилита, аналогичная `top`, но для дисковой подсистемы. Помогает разобраться, какой именно процесс вызывает высокую загрузку дисков.
			Установить: `sudo apt-get install -y iotop`

- `iftop` - показывает утилизацию сети в разрезе потоков данных.
			Установить: `sudo apt-get install -y iftop`

- `nethogs` - показывает утилизацию сети в разрезе процессов.
			Установить: `sudo apt-get install -y nethogs`

----

### Инструменты.

![](perftools.jpg)

![](bcc.jpg)


#### Инструмент node_exporter

Приведенные выше инструменты хороши для интерактивной оценки производительности системы, но они не решают проблемы сбора исторических данных.\
Решением является связка: `Prometheus + node_exporter`.

`node_exporter` - это компонент, который собирает метрики и предоставляет их системе мониторинга. В нашем случае системой мониторинга Prometheus.

>Prometheus будет рассмотрен в одной из следующих лекций.

	vagrant@netology1:~/node_exporter-1.0.1.linux-amd64$ curl -s localhost:9100/metrics
	2>/dev/null | grep node_filesystem_avail_bytes | grep mapper
	node_filesystem_avail_bytes{device="/dev/mapper/vgvagrant-root",fstype="ext4",mountpo
	int="/"} 6.075752448e+10
	
	vagrant@netology1:~/node_exporter-1.0.1.linux-amd64$ df -h | grep map
	per/dev/mapper/vgvagrant-root   62G  1.6G   57G   3% /


#### Ядро и модули

Версия ядра на машине:

	vagrant@vagrant:~$ uname -r
	5.4.0-91-generic

Версия и имя дистрибутива:

	vagrant@vagrant:~$ cat /etc/os-release
	NAME="Ubuntu"
	VERSION="20.04.3 LTS (Focal Fossa)"
	ID=ubuntu
	ID_LIKE=debian
	PRETTY_NAME="Ubuntu 20.04.3 LTS"
	VERSION_ID="20.04"
	HOME_URL="https://www.ubuntu.com/"
	SUPPORT_URL="https://help.ubuntu.com/"
	BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
	PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
	VERSION_CODENAME=focal
	UBUNTU_CODENAME=focal

	vagrant@vagrant:~$ lsb_release -a
	No LSB modules are available.
	Distributor ID: Ubuntu
	Description:    Ubuntu 20.04.3 LTS
	Release:        20.04
	Codename:       focal

#### Ядро и дистрибутив

Конфигурация сборки ядра:

	vagrant@vagrant:~$ cat /boot/config-$(uname -r) | head
	#
	# Automatically generated file; DO NOT EDIT.
	# Linux/x86 5.4.0-91-generic Kernel Configuration
	#
	#
	# Compiler: gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0
	#
	CONFIG_CC_IS_GCC=y
	CONFIG_GCC_VERSION=90300


Файл, содержащий все параметры, с которыми было скомпилировано ядро:

	vagrant@vagrant:~$ cat /boot/config-5.4.0-91-generic


В современных дистрибутивах ядра распространяются в форме пакетов:

	vagrant@vagrant:~$ dpkg -l | grep linux-image-5
	ii  linux-image-5.4.0-91-generic	5.4.0-91.102	amd64	Signed kernel image generic


#### Модули ядра

Одним из способом конфигурации ядра является использование модулей.\
Ядра дистрибутивов linux могут быть двух типов: монолитные или *микро*ядра.

>Микроядро содержит минимальный набор функций. Все функции, которые только можно, выносятся "наружу"\
>и потом их можно подгружать отдельно - используя в пользовательском пространстве или отдельными компонентами.

>Монолитное ядро - бинарный файл, который содержит в себе все необходимые и присущие ОС функции.

В то же время, несмотря на его "монолитность", существуют модули ядра, которые можно загружать во время работы.

Посмотреть загруженные модули можно командой `lsmod`.

Низкоуровневая команда для загрузки нового модуля – `insmod` (insert module),\
однако есть более удобный вариант – `modprobe`, который умеет автоматически подгружать зависимости\
и не требует указания пути.

Команда `modinfo` позволяет помотреть информацию по загруженному модулю.

Пример. Сначала выводим список подключенных модулей:
```sh
	vagrant@vagrant:~$ lsmod
	Module                  Size  Used by
	vboxsf                 81920  0
	dm_multipath           32768  0
	scsi_dh_rdac           16384  0
	scsi_dh_emc            16384  0
	scsi_dh_alua           20480  0
	vboxguest             348160  2 vboxsf
	input_leds             16384  0
	serio_raw              20480  0
	mac_hid                16384  0
	sch_fq_codel           20480  2
	msr                    16384  0
	ip_tables              32768  0
	x_tables               40960  1 ip_tables
	......
```
Теперь смотрим информацию по одному из них:
```sh
	vagrant@vagrant:~$ modinfo ip_tables
	filename:       /lib/modules/5.4.0-91-generic/kernel/net/ipv4/netfilter/ip_tables.ko
	alias:          ipt_icmp
	description:    IPv4 packet filter
	author:         Netfilter Core Team <coreteam@netfilter.org>
	license:        GPL
	srcversion:     2554B35100823A61CA983B4
	depends:        x_tables
	retpoline:      Y
	intree:         Y
	name:           ip_tables
	vermagic:       5.4.0-91-generic SMP mod_unload modversions
	sig_id:         PKCS#7
	signer:         Build time autogenerated kernel key
	sig_key:        18:8C:BE:F0:31:4B:0B:E0:E3:F8:76:71:AC:C9:6E:FF:B4:AB:57:F1
	sig_hashalgo:   sha512
	signature:............
```
Модули можно подгружать или выгружать без перезагрузки всей ОС.
Однако надо помнить, что после рестарта - все необходимые модули придётся подключать заново.

В файле `/etc/modprobe.d/mdadm.conf` перечислены все модули, которые загружаются при старте системы.


#### Настройка ядра - параметры загрузки






#### Система инициализации: systemd



#### Примечания.
Описание утилиты `sar` [в этой статье](https://linux-notes.org/sar-dlya-monitoringa-proizvoditel-nosti-sistemy/)
