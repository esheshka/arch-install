# **arch-install**


## 1. Создайте Live USB с поддержкой UEFI


## 2. Разбиение на разделы

Выведет существующие разделы. 

```
fdisk -l
```

Запомните как называется диск вашей системы: в большинстве случаев это ***sda***, но у меня ***nvme0n1***. 

**НЕ ПЕРЕПУТАЙТЕ С ШЛЕШКОЙ** (был случай)

Перейдем к форматированию (части с ***nvme0n1*** заменяйте на ***sda***)

```
fdisk /dev/nvme0n1
```

`d` удалить существующие разделы (по одному несколько раз).

`n` - создать новый раздел (ESP). ***Last sector*** поставьте `+512M`, остальное - по умолчанию.

Задайте ему тип ***ef*** (EFI) с помощью команды `t`. 

`n` - создать новый раздел (корневой). Все по умолчанию.

`w` - сохранить изменения.


## 3. Создание файловой системы

```
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2
```


## 4. Создание зеркала

```
pacman -Syy
pacman -S reflector
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
reflector -c "RU" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist
```


## 5. Установка Линукса

```
mount /dev/nvme0n1p2 /mnt
pacstrap /mnt base linux linux-firmware neovim
```


## 6. Сонфигурирование системы

```
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```


## 7. Настройка времени

```
timedatectl set-timezone Europe/Moscow
```


## 8. Настройка языка системы

В файле `/etc/locale.gen` раскоментируем строку `en_GB.UTF-8`

```
locale-gen
echo LANG=en_GB.UTF-8 > /etc/locale.conf
export LANG=en_GB.UTF-8
```


## 9. Интернет конфиг

```
echo esheshka > /etc/hostname
```

Создаем `/etc/hosts` и приводим к виду

```
127.0.0.1  localhost
::1  localhost
127.0.1.1  esheshka
```











## Остальное
yay -S xkb-switch-git
bindsym $mod+space exec "setxkbmap -query | grep -q 'us' && setxkbmap ru || setxkbmap us,ru"
