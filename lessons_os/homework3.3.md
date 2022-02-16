## Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

>1. Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной  программой,
	это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить
	`strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash`
	при старте. Вам нужно найти тот единственный, который относится именно к `cd`.

__Ответ: chdir("/tmp")__

---

>2. Попробуйте использовать команду `file` на объекты разных типов на файловой системе.
	Например:

    bash
    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64

	Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.

__Выполнение__

	vagrant@netology:~$ strace -e openat file /dev/tty
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libmagic.so.1", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/liblzma.so.5", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libbz2.so.1.0", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libz.so.1", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3
	openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3 #<================----------- Вот ссылка на искомую библиотеку.
	openat(AT_FDCWD, "/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY) = 3
	/dev/tty: character special (5/0)
	+++ exited with 0 +++

__Ответ: /etc/magic.mgc__

---

>3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален `(deleted в lsof)`, однако возможности
	сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает
	писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков
	предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).
	
__Выполнение__

```sh
vagrant@vagrant:~$ ll tmp/
total 16
drwxrwxr-x 2 vagrant vagrant 4096 Feb 14 18:44 ./
drwxr-xr-x 7 vagrant vagrant 4096 Feb 12 23:55 ../
-rw-rw-r-- 1 vagrant vagrant    0 Feb 14 18:44 det.log			# пустой файл для логов
vagrant@vagrant:~$ while true; do date +%s >> tmp/det.log; done &	# запускаем наш эмулятор записи логов
[1] 1027								# его PID
vagrant@vagrant:~$ renice -n 19 1027					# понижаем приоритет
1027 (process ID) old priority 0, new priority 19
vagrant@vagrant:~$ jobs -l						# проверяем, что всё работает
[1]+  1027 Running                 while true; do
    date +%s >> tmp/det.log;
done &
vagrant@vagrant:~$ ll tmp/
total 1272
drwxrwxr-x 2 vagrant vagrant    4096 Feb 14 18:44 ./
drwxr-xr-x 7 vagrant vagrant    4096 Feb 12 23:55 ../
-rw-rw-r-- 1 vagrant vagrant 1285537 Feb 14 18:49 det.log		# проверяем,что файл наполняется

vagrant@vagrant:~$ tmux attach-session -t 0				# отправляемся в сессию tmux, где открываем tmp/det.log
vagrant@vagrant:~$ nano tmp/det.log					# вот, собственно, открыли
vagrant@vagrant:~$ ps aux | grep vagrant				# возвращаемся из tmux и проверяем, что всё работает
.....
vagrant   459626  0.0  0.3  14500 11380 pts/1    S+   19:03   0:00 nano tmp/det.log	# вот он, открыт, да ещё и лог в него пишется одновременно
....
vagrant@vagrant:~$ lsof | grep det.log					# и вот тут охренительные новости! Оказывается tmp/det.log никем не открыт.
vagrant@vagrant:~$ 							# как такое возможно?
vagrant@vagrant:~$ ll tmp/
total 4300
drwxrwxr-x 2 vagrant vagrant    4096 Feb 14 19:03 ./
drwxr-xr-x 7 vagrant vagrant    4096 Feb 12 23:55 ../
-rw-rw-r-- 1 vagrant vagrant 4376977 Feb 14 19:12 det.log		# он продолжает пополняться
-rw-rw-r-- 1 vagrant vagrant    1024 Feb 14 19:03 .det.log.swp		# он открыт, но не открыт????????? КАК????
...
```
__Я даже воспроизвести это не смог. Извините.__\
Мне нужны объяснения. Я не понимаю, что происходит!

------
	
	
>4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?
	
__Выполнение__

Зомби-процессы не занимают и не использют никаких ресурсов ОС. Они просто числятся в списках все процессов и не более того.
Ниже скрин команды top, показывающий зомби процесс:

![](https://github.com/AleksandrNetology/Netology/blob/main/lessons_os/zombie_res.JPG)

Как видно, зомби-процесс не тратит абсолютно никаких ресурсов системы - расход всех ресурсов равен нулю.

------
	
	
>5. В iovisor BCC есть утилита `opensnoop`:
```sh
    bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
```
На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools`
для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).
	
__Выполнение__

![](https://github.com/AleksandrNetology/Netology/blob/main/lessons_os/opensnoop.jpg)

------
	
>6. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается
	альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.

__Выполнение__

Нам под описание задачи подошёл системный вызов `uname`, в описании к кторому и нашлась искомая строка про `/proc`:

	`Part of the utsname information is  also  accessible  via  /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.

Т.о. альтернативное местоположение: `/proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}`

------

>7. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:

    bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#

__Выполнение__

Для начала выясняем, что есть что:

`&&` - в данном случае используется для объединения команд таким образом, что следующая команда запускается только тогда,
когда предыдущая команда завершилась с кодом возврата 0, т.е. успешно;

`;` - просто разделяет последовательно выполняющиеся команды. Ей безразлично, с каким кодом возврата завершилась
предыдущая команда.

`set -e` (`errexit`): команда прерывает работу сценария при появлении первой же ошибки, т.е. когда код завершения не равен 0.

	Есть ли смысл использовать в bash `&&`, если применить `set -e`?

В мануале сказано, что вместе их использовать можно, но довольно много разных "__НО__".
![](https://github.com/AleksandrNetology/Netology/blob/main/lessons_os/set-e.jpg)

На самом деле, я думаю, всё зависит от поставленной задачи, предпочтительного способа её решения и того, кто решает задачу.

------

>8. Из каких опций состоит режим bash `set -euxo pipefail` и почему его хорошо было бы использовать в сценариях?
	Почему его хорошо было бы использовать в сценариях?

__Выполнение__

Команда состоит из отдельных опций: `-e`, `-u`, `-x` и `-o pipefail`.\
Инфу по каждому можно найти в `man`, но не стоит впустую тратить время - очень хорошо объяснено в статье (см. примечания).\
Цитата из [статьи](http://redsymbol.net/articles/unofficial-bash-strict-mode/):

	С этими настройками некоторые распространенные ошибки вызовут немедленный сбой скрипта, явный и громкий.
	В противном случае вы можете получить скрытые баги, которые обнаруживаются только тогда, когда они взрываются в продакшене.

__Значит__, режим bash `set -euxo pipefail` предназначен для выявления скрытых ошибок некоего сценария.

------

>9. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе.
	В `man ps` ознакомьтесь (`/PROCESS STATE CODES`) что значат дополнительные к основной заглавной буквы статуса
	процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

__Выполнение__
```sh
vagrant@vagrant:~$ ps -o stat
STAT
Ss
R+
vagrant@vagrant:~$ ps a
	PID TTY      STAT   TIME COMMAND
  13829 tty6     Ss+    0:00 /sbin/agetty -o -p -- \u --noclear tty6 linux
  13860 tty2     Ss+    0:00 /sbin/agetty -o -p -- \u --noclear tty2 linux
  39747 tty1     Ss+    0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
  39947 pts/0    Ss     0:00 -bash
  40195 pts/0    R+     0:00 ps a
```

Процессы:\
__S{s,l,+}__ - interruptible sleep - это семейство спящих процессов, ожидающих какого-то события;\
__R__ - running\runnable - исполняется.

Значения дополнительных символов и букв:
```
<    - процесс с высоким приоритетом;
N    - процесс с низким приоритетом;
L    - у процесса есть залоченные в памяти страницы (для ввода-вывода в реальном времени и пользовательского ввода-вывода);
s    - лидер сессии; (Про эту хрень в примечаниях)
l    - мультипоточный процесс;
+    - находится в группе процессов переднего плана.
```
------

#### Примечания и восхищения.

Интересное кино: как искать инфу по `set -e` в мануале? По ключевому слову `set`? По ключевому `-e`?\
Если искать по `&&` и на три тысячи двести пятьдесят первой строчке можно попасть в нужный абзац...\
Никогда бы не нашёл, если б не гугл и ссылка на man с адекватным поиском. Как это искать без гугла???\
Искать надо по сочетанию параметров этой команды: `abefhkmnptuvxBCEHPT`?? Других вариантов я пока не нашёл.

`man` с адекватным поиском: https://man7.org/linux/man-pages/

Про `set -euxo pipefail` две ссылки:
1. [кратко](https://gist.github.com/mohanpedala/1e2ff5661761d3abd0385e8223e16425#set--e--u--x--o-pipefail);
2. [подробно от автора](http://redsymbol.net/articles/unofficial-bash-strict-mode/).

Про дополнительное обозначение статуса процесса, а именно `l` - [лидер сессии](https://qastack.ru/unix/18166/what-are-session-leaders-in-ps).
