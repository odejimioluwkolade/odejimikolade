---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Лабораторная работа №13 — Настройка NFS
author:
  - Одеджими Олуваколаде
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 16 марта 2026

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Цель работы

Приобретение практических навыков по настройке сервера NFS (Network File System)
для удалённого доступа к ресурсам в операционной системе AlmaLinux/RHEL
в среде виртуализации Vagrant.

**Задания:**

1. Настроить NFS-сервер: установить `nfs-utils`, создать дерево `/srv/nfs`,
   настроить SELinux, запустить `nfs-server.service`
2. Подмонтировать на клиенте: смонтировать `/srv/nfs` в `/mnt/nfs`,
   настроить `fstab` с опцией `_netdev`, проверить `remote-fs.target`
3. Подключить каталоги: bind-монтирование `/var/www` и `~/common` к дереву NFS;
   настроить автоматизацию Vagrant

# Настройка сервера NFSv4

## Установка и конфигурация

```bash
dnf -y install nfs-utils
mkdir -p /srv/nfs
# /etc/exports:
/srv/nfs *(ro)
semanage fcontext -a -t nfs_t \
  "/srv/nfs(/.*)?"
restorecon -vR /srv/nfs
systemctl start nfs-server.service
systemctl enable nfs-server.service
```

## Межсетевой экран и проверка

```bash
firewall-cmd --add-service=nfs
firewall-cmd --add-service=nfs --permanent
firewall-cmd --reload
# Добавление mountd и rpc-bind:
firewall-cmd --add-service=mountd \
  --add-service=rpc-bind
firewall-cmd --add-service=mountd \
  --add-service=rpc-bind --permanent
firewall-cmd --reload
```

Проверка с клиента:

```
showmount -e server.kolade.net
→ Export list for server.kolade.net: /srv/nfs *
```

# Монтирование NFS на клиенте

## Монтирование и проверка

```bash
# Установка на клиенте:
dnf -y install nfs-utils
# Создание точки монтирования:
mkdir -p /mnt/nfs
mount server.kolade.net:/srv/nfs /mnt/nfs
# Проверка:
mount | grep nfs
→ server.kolade.net:/srv/nfs on /mnt/nfs
  type nfs4 (rw,relatime,vers=4.2,...)
```

## Автомонтирование /etc/fstab

```bash
# Запись в /etc/fstab (строка 17):
server.kolade.net:/srv/nfs \
  /mnt/nfs nfs _netdev 0 0
# _netdev: ждать инициализации сети
# 0 0: отключить dump и fsck

# Проверка remote-fs.target:
systemctl status remote-fs.target
→ active (running)
→ Active since 2026-03-09 13:32:50
```

`ls /mnt/nfs/` → каталог `www` виден сразу после монтирования;
после перезапуска клиента ресурс монтируется автоматически.

# Подключение каталогов к дереву NFS

## Каталог веб-сервера (/var/www)

```bash
# Bind-монтирование:
mkdir -p /srv/nfs/www
mount -o bind /var/www /srv/nfs/www
# /etc/exports добавить:
/srv/nfs/www 192.168.0.0/16(rw)
# /etc/fstab сервера (строка 17):
/var/www /srv/nfs/www none bind 0 0
exportfs -r
```

## Каталог пользователя kolade

```bash
# Сервер — создание ~/common:
mkdir -p -m 700 ~/common
touch kolade@server.txt
mkdir -p /srv/nfs/home/kolade
mount -o bind ~/common /srv/nfs/home/kolade
# /etc/exports добавить:
/srv/nfs/home/kolade 192.168.0.0/16(rw)
# /etc/fstab (строка 18):
~/common /srv/nfs/home/kolade none bind 0 0
```

Итоговый `/etc/exports`:

```
/srv/nfs *(ro)
/srv/nfs/www 192.168.0.0/16(rw)
/srv/nfs/home/kolade 192.168.0.0/16(rw)
```

# Проверка прав доступа

## Клиент — пользователь kolade

```bash
cd /mnt/nfs/home/kolade
ls -l
-rw-r--r-- 1 kolade kolade kolade@server.txt
touch kolade@client.txt
ls -l
-rw-r--r-- kolade@client.txt
-rw-r--r-- kolade@server.txt
```

**Сервер — проверка ~/common:**

```bash
ls -l ~/common
-rw-r--r-- kolade@client.txt
-rw-r--r-- kolade@server.txt
# root на клиенте пытается войти:
cd /mnt/nfs/home/kolade
→ -bash: Permission denied  # Права 700 работают!
```

# Скрипты Vagrant

## provision/server/nfs.sh

```bash
#!/bin/bash
dnf -y install nfs-utils
cp -R /vagrant/provision/server/nfs/etc/* /etc
restorecon -vR /etc
firewall-cmd --add-service=nfs --permanent
firewall-cmd --add-service=mountd \
  --add-service=rpc-bind --permanent
firewall-cmd --reload
mkdir -p /srv/nfs
semanage fcontext -a -t nfs_t "/srv/nfs(/.*)?"
restorecon -vR /srv/nfs
mkdir -p /srv/nfs/www
mount -o bind /var/www /srv/nfs/www
echo "/var/www /srv/nfs/www none bind 0 0" >> /etc/fstab
mkdir -p /srv/nfs/home/kolade
mkdir -p -m 700 /home/kolade/common
chown kolade:kolade /home/kolade/common
mount -o bind /home/kolade/common /srv/nfs/home/kolade
systemctl enable nfs-server && systemctl start nfs-server
```

## provision/client/nfs.sh

```bash
#!/bin/bash
dnf -y install nfs-utils
mkdir -p /mnt/nfs
mount server.kolade.net:/srv/nfs /mnt/nfs
echo "server.kolade.net:/srv/nfs \
  /mnt/nfs nfs _netdev 0 0" >> /etc/fstab
restorecon -vR /etc
```

**Vagrantfile:**

```ruby
server.vm.provision "server nfs",
  type: "shell", preserve_order: true,
  path: "provision/server/nfs.sh"
client.vm.provision "client nfs",
  type: "shell", preserve_order: true,
  path: "provision/client/nfs.sh"
```

# Скриншоты

## Файл /etc/exports — начальная конфигурация

![Файл /etc/exports — начальная конфигурация /srv/nfs \*(ro)](Screenshot_1.png){ #fig:001 width=80% }

## Сервер: установка nfs-utils, SELinux, firewall-cmd

![Установка nfs-utils, настройка /etc/exports, SELinux, запуск nfs-server, firewall-cmd](Screenshot_2.png){ #fig:002 width=80% }

## Клиент: монтирование NFS, mount | grep nfs (NFSv4)

![Клиент: монтирование NFS, проверка mount | grep nfs (NFSv4)](Screenshot_5.png){ #fig:003 width=80% }

## Клиент: remote-fs.target active, ls /mnt/nfs/ показывает www

![Клиент: remote-fs.target active, ls /mnt/nfs/ показывает www](Screenshot_7.png){ #fig:004 width=80% }

## Сервер: ~/common — двусторонний доступ

![Сервер: ~/common — файлы от сервера и клиента (двусторонний доступ)](Screenshot_16.png){ #fig:005 width=80% }

# Выводы

## Вывод

- Установлен `nfs-utils`; создан корень `/srv/nfs` с SELinux-контекстом `nfs_t`;
  запущен `nfs-server.service`
- Межсетевой экран настроен: открыты `nfs`, `mountd` и `rpc-bind` —
  без этого клиент получает ошибку RPC
- Клиент смонтировал `/srv/nfs` в `/mnt/nfs`; опция `_netdev` в `/etc/fstab`
  гарантирует монтирование после загрузки сети
- Каталог `/var/www` и домашний `~/common` подключены через bind-монтирование —
  двусторонний обмен файлами подтверждён
- Права 700 на `~/common` корректно запрещают доступ `root` на клиенте
  (root_squash работает по умолчанию)
- Скрипты Vagrant `nfs.sh` для сервера и клиента автоматизируют
  полное развёртывание NFS
