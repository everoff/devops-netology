Домашная работа "Файловые системы системы"

FS-1

1.
Изучил, и правда, интересное решение.

2.
Нет, не могут, тк ссылки наследуют свойства объекта, на который они ссылаются.
Если попробовать сделать.
vagrant@vagrant:~$ touch homework/fs-1
vagrant@vagrant:~$ cd homework/
vagrant@vagrant:~/homework$ ln fs-1 fs_hardlink
vagrant@vagrant:~/homework$ ls -ilh
total 24K
1055929 -rw-rw-r-- 2 vagrant vagrant  0 Feb  6 15:59 fs-1
1055929 -rw-rw-r-- 2 vagrant vagrant  0 Feb  6 15:59 fs_hardlink
vagrant@vagrant:~/homework$ chmod 0777 fs_hardlink
vagrant@vagrant:~/homework$ ls -ilh
total 24K
1055929 -rwxrwxrwx 2 vagrant vagrant  0 Feb  6 15:59 fs-1
1055929 -rwxrwxrwx 2 vagrant vagrant  0 Feb  6 15:59 fs_hardlink

3.
Выполнено
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk
sdc                         8:32   0  2.5G  0 disk

4.
Выполнено.
Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux

5.
Выполнено.
vagrant@vagrant:~$ sudo sfdisk -d /dev/sdb| sudo sfdisk /dev/sdc
Checking that no-one is using this disk right now ... OK

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0x0e38bd08.
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: dos
Disk identifier: 0x0e38bd08

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

Результат sdc:
Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux

6.
Выполнено.
vagrant@vagrant:~$ sudo mdadm --create --verbose /dev/md1 -l 1 -n 2 /dev/sd{b1,c1}
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: size set to 2094080K
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.

7.
Выполнено.
vagrant@vagrant:~$ sudo mdadm --create --verbose /dev/md0 -l 0 -n 2 /dev/sd{b2,c2}
mdadm: chunk size defaults to 512K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.

8.
root@vagrant:~# pvcreate /dev/md1 /dev/md0
  Physical volume "/dev/md1" successfully created.
  Physical volume "/dev/md0" successfully created.

9.
Выполнено.
root@vagrant:~# vgcreate vg1 /dev/md1 /dev/md0
  Volume group "vg1" successfully created
root@vagrant:~# vgdisplay
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
  VG Name               vg1
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
  VG UUID               dh5r2f-8jrS-SI0j-X4La-MEAp-Itdt-y26ceB

10.
Выполнено.
root@vagrant:~# lvcreate -L 100M vg1 /dev/md0
  Logical volume "lvol0" created.
root@vagrant:~# vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  ubuntu-vg   1   1   0 wz--n- <63.00g <31.50g
  vg1         2   1   0 wz--n-  <2.99g   2.89g
root@vagrant:~# lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
  lvol0     vg1       -wi-a----- 100.00m

11.
Выполнено.
root@vagrant:~# mkfs.ext4 /dev/vg1/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done

12.
Выполнено.
root@vagrant:~# mkdir /tmp/new
root@vagrant:~# mount /dev/vg1/lvol0 /tmp/new/

13.
Выполнено.
root@vagrant:~# wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-02-07 19:07:20--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22244348 (21M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz             100%[==============================================>]  21.21M  3.94MB/s    in 4.8s

2022-02-07 19:07:25 (4.42 MB/s) - ‘/tmp/new/test.gz’ saved [22244348/22244348]

14.
Выполнено.
root@vagrant:~# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 70.3M  1 loop  /snap/lxd/21029
loop1                       7:1    0 32.3M  1 loop  /snap/snapd/12704
loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop  /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md0                     9:0    0 1018M  0 raid0
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md0                     9:0    0 1018M  0 raid0
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new

15.
Выполнено.
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0

16.
Выполнено
root@vagrant:~# pvmove /dev/md0
  /dev/md0: Moved: 24.00%
  /dev/md0: Moved: 100.00%

17.
Выполнено.
root@vagrant:~# mdadm /dev/md1 --fail /dev/sdc1
mdadm: set /dev/sdc1 faulty in /dev/md1

18.
Выполнено.
root@vagrant:~# dmesg | grep md1
[ 1027.412445] md/raid1:md1: not clean -- starting background reconstruction
[ 1027.412447] md/raid1:md1: active with 2 out of 2 mirrors
[ 1027.412468] md1: detected capacity change from 0 to 2144337920
[ 1027.413587] md: resync of RAID array md1
[ 1038.310179] md: md1: resync done.
[ 4047.161345] md/raid1:md1: Disk failure on sdc1, disabling device.
               md/raid1:md1: Operation continuing on 1 devices.

19.
Выполнено.
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0

20.
Выполнено.
maksim@MacBook-Pro vagrant % vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...

