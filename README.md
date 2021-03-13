# Otus5
## Загрузка системы.

Работа с загрузчиком
1. Попасть в систему без пароля несколькими способами
2. Установить систему с LVM, после чего переименовать VG
3. Добавить модуль в initrd

1. Попасть с систему без пароля

При загрузке системы, в момент выбора ядра, нажать е. Далее в строке, начинающейся с linux16 стереть почти все параметры загрузки, кроме /boot (ядра) и /root. Добавить rd.break в конце, поменять ro на rw, также отключить selinux добавив selinux=0. 
Попадаем в emergency mode, меняем пароль администратора с помощью набора команд:

```
 mount -o remount,rw /sysroot
 chroot /sysroot
 passwd root
 touch /.autorelabel
```
2. Меняем имя volume group

Первым делом посмотрим текущее состояние системы:

```
 sudo vgs
```

Приступим к переименованию:

```
 sudo vgrename volumegroup otus
```

Далее правим /etc/fstab

```
 sudo mcedit /etc/fstab
 /dev/otus/volumegroup /otus ext4 defaults 0 0
```

Пересоздаем initrd image, чтобы он знал новое название Volume Group:

```
 sudo mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
```

Перезагружаемся.

3. Добавить модуль

Скрипты модулей хранятся в каталоге /usr/lib/dracut/modules.d/. Длā того чтобы
добавить свой модуль создаем там папку с именем 01test:

```
 sudo mkdir /usr/lib/dracut/modules.d/01test
```

В нее поместим два скрипта: module-setup.sh и test.sh
Пересобираем образ initrd:

```
 sudo  mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
```

Далее отредактируем grub.cfg убрав опции quite и rghb.
Перезагружаемся.
