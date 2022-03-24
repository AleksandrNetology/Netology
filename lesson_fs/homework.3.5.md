# Домашнее задание к занятию "3.5. Файловые системы"

1. Узнайте о [__sparse__](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) - разрежённых файлах.

=Выполнение=

----

Узнал.

=Выполнено=

----

2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

=Выполнение=

----

Основным идентификатором файла является индексный дескриптор файла - `inode`.\
`inode` - это область данный в файловой системе, где хранятся, __в том числе, данные о правах доступа к файлу__.\
Таким образом, жёсткие ссылки, которые __только указывают__ на `inode` не хранят в себе права доступа и, следовательно, сами по себе не могут иметь никаких __личных__ прав доступа.

=Выполнено=

----

3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

    ```bash
    Vagrant.configure("2") do |config|
      config.vm.box = "bento/ubuntu-20.04"
      config.vm.provider :virtualbox do |vb|
        lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
        lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
        vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
        vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
      end
    end
    ```

    Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.
	
=Выполнение=

----

Создал новую ВМ.\
Проверяем, что получилось в части дисков:
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
loop1                       7:1    0 70.3M  1 loop /snap/lxd/21029
loop2                       7:2    0 32.3M  1 loop /snap/snapd/12704
loop3                       7:3    0 55.5M  1 loop /snap/core18/2344
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk
sdc                         8:32   0  2.5G  0 disk
```

=Выполнено=

----

4. Используя `fdisk`, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x29db4d3f.

Command (m for help): g
Created a new GPT disklabel (GUID: 6B593D5D-40B5-8443-B685-99FA5D9459A2).

Command (m for help): n
Partition number (1-128, default 1):
First sector (2048-5242846, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242846, default 5242846): +2G

Created a new partition 1 of type 'Linux filesystem' and of size 2 GiB.

Command (m for help): n
Partition number (2-128, default 2):
First sector (4196352-5242846, default 4196352):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242846, default 5242846):

Created a new partition 2 of type 'Linux filesystem' and of size 511 MiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
Проверяем результат:
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
...
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
└─sdb2                      8:18   0  511M  0 part
sdc                         8:32   0  2.5G  0 disk
```

=Выполнено=

----

5. Используя `sfdisk`, перенесите данную таблицу разделов на второй диск.

=Выполнение=

----

```sh
vagrant@vagrant:~$ sudo -i
root@vagrant:~# sfdisk -d /dev/sdb | sfdisk --force /dev/sdc
Checking that no-one is using this disk right now ... OK

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: A5AC933F-4C00-A043-8E49-7B47287E1D07

Old situation:

>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new GPT disklabel (GUID: C2B9109C-6E40-CB41-9C5E-8198F7F6AE14).
/dev/sdc1: Created a new partition 1 of type 'Linux filesystem' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Linux filesystem' and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: gpt
Disk identifier: C2B9109C-6E40-CB41-9C5E-8198F7F6AE14

Device       Start     End Sectors  Size Type
/dev/sdc1     2048 4196351 4194304    2G Linux filesystem
/dev/sdc2  4196352 5242846 1046495  511M Linux filesystem

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
Проверяем:
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
loop1                       7:1    0 70.3M  1 loop /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop /snap/core18/2344
loop4                       7:4    0 43.6M  1 loop /snap/snapd/15177
loop5                       7:5    0 61.9M  1 loop /snap/core20/1376
loop6                       7:6    0 67.8M  1 loop /snap/lxd/22753
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
└─sdb2                      8:18   0  511M  0 part
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
└─sdc2                      8:34   0  511M  0 part
```

=Выполнено=

----

6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo mdadm -C /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```
Проверяем:
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
...
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
```

=Выполнено

----

7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo mdadm -C /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
mdadm: /dev/sdb2 appears to be part of a raid array:
       level=raid0 devices=2 ctime=Thu Mar 24 21:59:15 2022
mdadm: /dev/sdc2 appears to be part of a raid array:
       level=raid0 devices=2 ctime=Thu Mar 24 21:59:15 2022
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
```
Проверяем:
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
...
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md1                     9:1    0 1017M  0 raid0
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md1                     9:1    0 1017M  0 raid0
```
=Выполнено=

----

8. Создайте 2 независимых PV на получившихся md-устройствах.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo pvcreate /dev/md0 /dev/md1
  Physical volume "/dev/md0" successfully created.
  Physical volume "/dev/md1" successfully created.
```
=Выполнено=

----

9. Создайте общую volume-group на этих двух PV.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo vgcreate vg0 /dev/md0 /dev/md1
  Volume group "vg0" successfully created
vagrant@vagrant:~$ sudo vgdisplay
  --- Volume group ---
  VG Name               ubuntu-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  2
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <63.00 GiB
  PE Size               4.00 MiB
  Total PE              16127
  Alloc PE / Size       8064 / 31.50 GiB
  Free  PE / Size       8063 / <31.50 GiB
  VG UUID               aK7Bd1-JPle-i0h7-5jJa-M60v-WwMk-PFByJ7

  --- Volume group ---
  VG Name               vg0
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               <2.99 GiB
  PE Size               4.00 MiB
  Total PE              765
  Alloc PE / Size       0 / 0
  Free  PE / Size       765 / <2.99 GiB
  VG UUID               fP9zVE-34Le-VAU5-WoRz-DmeL-4EdZ-K9ZDlf
```

=Выполнено=

----

10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo lvcreate -L 100M vg0 /dev/md1
  Logical volume "lvol0" created.
vagrant@vagrant:~$ sudo vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  ubuntu-vg   1   1   0 wz--n- <63.00g <31.50g
  vg0         2   1   0 wz--n-  <2.99g   2.8
vagrant@vagrant:~$ sudo lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
  lvol0     vg0       -wi-a----- 100.00m
```

=Выполнено=

----

11. Создайте `mkfs.ext4` ФС на получившемся LV.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo mkfs.ext4 /dev/vg0/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```

=Выполнено=

----

12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.

=Выполнение=

----
```sh
vagrant@vagrant:~$ ll
total 36
drwxr-xr-x 4 vagrant vagrant 4096 Dec 19 19:45 ./
drwxr-xr-x 3 root    root    4096 Dec 19 19:42 ../
-rw-r--r-- 1 vagrant vagrant  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 vagrant vagrant 3771 Feb 25  2020 .bashrc
drwx------ 2 vagrant vagrant 4096 Dec 19 19:42 .cache/
-rw-r--r-- 1 vagrant vagrant  807 Feb 25  2020 .profile
drwx------ 2 vagrant root    4096 Dec 19 19:44 .ssh/
-rw-r--r-- 1 vagrant vagrant    0 Dec 19 19:42 .sudo_as_admin_successful
-rw-r--r-- 1 vagrant vagrant    6 Dec 19 19:42 .vbox_version
-rw-r--r-- 1 root    root     180 Dec 19 19:44 .wget-hsts
vagrant@vagrant:~$ mkdir /tmp/new
vagrant@vagrant:~$ sudo mount /dev/vg0/lvol0 /tmp/new
```

=Выполнено=

----

13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.

=Выполнение=

----
```sh
vagrant@vagrant:/tmp/new$ ll
total 24
drwxr-xr-x  3 root root  4096 Mar 24 22:28 ./
drwxrwxrwt 11 root root  4096 Mar 24 22:29 ../
drwx------  2 root root 16384 Mar 24 22:28 lost+found/
vagrant@vagrant:/tmp/new$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-03-24 22:33:45--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22422327 (21M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz     100%[=====================>]  21.38M  10.5MB/s    in 2.0s

2022-03-24 22:33:47 (10.5 MB/s) - ‘/tmp/new/test.gz’ saved [22422327/22422327]

vagrant@vagrant:/tmp/new$ ll
total 21924
drwxr-xr-x  3 root root     4096 Mar 24 22:33 ./
drwxrwxrwt 11 root root     4096 Mar 24 22:29 ../
drwx------  2 root root    16384 Mar 24 22:28 lost+found/
-rw-r--r--  1 root root 22422327 Mar 24 21:48 test.gz
```

=Выполнено=

----

14. Прикрепите вывод `lsblk`.

=Выполнение=

----
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
...
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md1                     9:1    0 1017M  0 raid0
    └─vg0-lvol0           253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md1                     9:1    0 1017M  0 raid0
    └─vg0-lvol0           253:1    0  100M  0 lvm   /tmp/new
```

=Выполнено=

----

15. Протестируйте целостность файла:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo gzip -t /tmp/new/test.gz
vagrant@vagrant:~$ echo $?
0
```

=Выполнено=

----

16. Используя `pvmove`, переместите содержимое PV с RAID0 на RAID1.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo pvmove -i1 /dev/md1 /dev/md0
  /dev/md1: Moved: 64.00%
  /dev/md1: Moved: 100.00%
```
Проверяем:
```sh
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
...
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
│   └─vg0-lvol0           253:1    0  100M  0 lvm   /tmp/new
└─sdb2                      8:18   0  511M  0 part
  └─md1                     9:1    0 1017M  0 raid0
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md0                     9:0    0    2G  0 raid1
│   └─vg0-lvol0           253:1    0  100M  0 lvm   /tmp/new
└─sdc2                      8:34   0  511M  0 part
  └─md1                     9:1    0 1017M  0 raid0
```

=Выполнено=

----

17. Сделайте `--fail` на устройство в вашем RAID1 md.

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo mdadm /dev/md0 --fail /dev/sdb1
mdadm: set /dev/sdb1 faulty in /dev/md0

vagrant@vagrant:~$ sudo mdadm -D /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Thu Mar 24 21:49:58 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Thu Mar 24 22:47:18 2022
             State : clean, degraded
    Active Devices : 1
   Working Devices : 1
    Failed Devices : 1
     Spare Devices : 0

Consistency Policy : resync

              Name : vagrant:0  (local to host vagrant)
              UUID : c30645fe:444e2a3c:9be88383:39248a60
            Events : 19

    Number   Major   Minor   RaidDevice State
       -       0        0        0      removed
       1       8       33        1      active sync   /dev/sdc1

       0       8       17        -      faulty   /dev/sdb1
```

=Выполнено=

----

18. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.

=Выполнение=

----
```sh
[ 9479.120852] md/raid1:md0: Disk failure on sdb1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
```

=Выполнено=

----

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

=Выполнение=

----
```sh
vagrant@vagrant:~$ sudo gzip -t /tmp/new/test.gz
vagrant@vagrant:~$ echo $?
0
```

=Выполнено=

----

20. Погасите тестовый хост, `vagrant destroy`.

=Выполнение=

----

Сделал.

=Выполнено=

----

### Примечания


Команда [__fdisk__](https://losst.ru/komanda-fdisk-v-linux) в Linux.\
[__Работа с файловой системой в Linux__](https://losst.ru/rabota-s-fajlovoj-sistemoj-linux)\
Утилита [__mdadm__](https://ru.wikibooks.org/wiki/Mdadm) - подробное описание с примерами.




