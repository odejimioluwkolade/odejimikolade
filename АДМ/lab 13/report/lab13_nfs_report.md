---
## Front matter
title: "Отчёт по лабораторной работе 13"
subtitle: "Настройка NFS"
author: "Одеджими Олуваколаде"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: false # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Цель данной лабораторной работы — приобретение практических навыков по настройке сервера
NFS (Network File System) для удалённого доступа к ресурсам в операционной системе
AlmaLinux/RHEL в среде виртуализации Vagrant.

# Задание

В ходе лабораторной работы необходимо выполнить следующие задания:

1. Установить и настроить сервер NFSv4.
2. Подмонтировать удалённый ресурс на клиенте.
3. Подключить каталог с контентом веб-сервера к дереву NFS.
4. Подключить каталог для удалённой работы пользователя `kolade` к дереву NFS.
5. Написать скрипты для Vagrant, фиксирующие действия по установке и настройке сервера
   NFSv4. Внести изменения в Vagrantfile.

# Описание результатов выполнения задания

## Настройка сервера NFSv4

На скриншоте 1 показан файл `/etc/exports` на сервере с начальной записью `/srv/nfs *(ro)` —
корень дерева NFS, доступный всем хостам только для чтения.

![Файл /etc/exports — начальная конфигурация: /srv/nfs \*(ro)](Screenshot_1.png){ #fig:001 width=80% }

На скриншоте 2 показан полный процесс начальной настройки сервера NFS: установка пакета
`dnf -y install nfs-utils`, создание каталога `mkdir -p /srv/nfs`, редактирование
`/etc/exports`, настройка SELinux (`semanage fcontext`, `restorecon`), запуск и включение
`nfs-server.service`, а также настройка `firewall-cmd --add-service=nfs`.

![Установка nfs-utils, настройка /etc/exports, SELinux, запуск nfs-server, firewall-cmd](Screenshot_2.png){ #fig:002 width=80% }

На скриншоте 3 показан вывод `lsof | grep UDP` на сервере — видны работающие службы
`rpcbind` (порты sun-rpc) и `nfs`. Также показано добавление `mountd` и `rpc-bind` в
`firewall-cmd`.

![lsof | grep UDP на сервере, добавление mountd и rpc-bind в firewall-cmd](Screenshot_3.png){ #fig:003 width=80% }

На скриншоте 4 показан клиент: завершение установки `nfs-utils`, первая попытка
`showmount -e server.kolade.net` (ошибка RPC до настройки `firewall-cmd`) и успешный
результат после добавления правил брандмауэра.

![Клиент: установка nfs-utils, showmount (ошибка и успех после firewall-cmd)](Screenshot_4.png){ #fig:004 width=80% }

## Монтирование NFS на клиенте

На скриншоте 5 показано монтирование на клиенте: создание каталога `mkdir -p /mnt/nfs`,
монтирование `server.kolade.net:/srv/nfs /mnt/nfs` и проверка через `mount | grep nfs`
(тип `nfs4`, версия 4.2).

![Клиент: монтирование NFS, проверка mount | grep nfs](Screenshot_5.png){ #fig:005 width=80% }

На скриншоте 6 показан файл `/etc/fstab` на клиенте с добавленной строкой в строке 17:
`server.kolade.net:/srv/nfs /mnt/nfs nfs _netdev 0 0`.

![Клиент: /etc/fstab — добавлена запись NFS (строка 17)](Screenshot_6.png){ #fig:006 width=80% }

На скриншоте 7 показана проверка автоматического монтирования: команды `mount -a`,
`systemctl status remote-fs.target` (active) и `ls /mnt/nfs/` — виден каталог `www`.

![Клиент: remote-fs.target active, ls /mnt/nfs/ показывает www](Screenshot_7.png){ #fig:007 width=80% }

## Подключение каталога веб-сервера к дереву NFS

На скриншоте 8 показан файл `/etc/exports` на сервере с двумя строками: `/srv/nfs *(ro)` и
`/srv/nfs/www 192.168.0.0/16(rw)`.

![Сервер: /etc/exports — добавлен экспорт /srv/nfs/www 192.168.0.0/16(rw)](Screenshot_8.png){ #fig:008 width=80% }

На скриншоте 9 показан файл `/etc/fstab` на сервере с добавленной строкой 17:
`/var/www /srv/nfs/www none bind 0 0`.

![Сервер: /etc/fstab — запись bind-монтирования /var/www в /srv/nfs/www (строка 17)](Screenshot_9.png){ #fig:009 width=80% }

На скриншоте 10 показаны: `firewall-cmd` с добавлением `mountd` и `rpc-bind`, создание
каталога `mkdir /srv/nfs/www`, bind-монтирование `/var/www` в `/srv/nfs/www`, затем
создание `/srv/nfs/home/kolade` с bind-монтированием `~/common`, и команда `exportfs -r`.

![Сервер: настройка bind-монтирования www и home/kolade, exportfs -r](Screenshot_10.png){ #fig:010 width=80% }

## Подключение каталога для работы пользователя

На скриншоте 11 показана работа пользователя `kolade` на сервере: создание каталога
`~/common` с правами 700 и создание файла `kolade@server.txt`.

![Сервер: создание ~/common и файла kolade@server.txt](Screenshot_11.png){ #fig:011 width=80% }

На скриншоте 12 показан файл `/etc/exports` с тремя строками: `/srv/nfs *(ro)`,
`/srv/nfs/www 192.168.0.0/16(rw)`, `/srv/nfs/home/kolade 192.168.0.0/16(rw)`.

![Сервер: /etc/exports — три записи, включая /srv/nfs/home/kolade](Screenshot_12.png){ #fig:012 width=80% }

На скриншоте 13 показан файл `/etc/fstab` на сервере с двумя добавленными записями:
строка 17 — `/var/www /srv/nfs/www none bind 0 0`,
строка 18 — `/home/kolade/common /srv/nfs/home/kolade none bind 0 0`.

![Сервер: /etc/fstab — строки 17 и 18 с bind-монтированием](Screenshot_13.png){ #fig:013 width=80% }

На скриншоте 14 показан полный процесс настройки на сервере: `mkdir /srv/nfs/home/kolade`,
bind-монтирование `~/common` в `/srv/nfs/home/kolade`, добавление записи в `/etc/fstab`,
команда `exportfs -r`.

![Сервер: полный процесс настройки home/kolade в NFS-дерево](Screenshot_14.png){ #fig:014 width=80% }

На скриншоте 15 показана работа на клиенте от пользователя `kolade`: переход в
`/mnt/nfs/home/kolade/`, просмотр `ls -l` (виден `kolade@server.txt`), создание
`kolade@client.txt`, а также попытка доступа от `root` — получен отказ (права 700).

![Клиент: создание kolade@client.txt в /mnt/nfs/home/kolade/; root отказано](Screenshot_15.png){ #fig:015 width=80% }

На скриншоте 16 показан сервер — каталог `~/common` пользователя `kolade`: `ls -l`
отображает оба файла: `kolade@server.txt` и `kolade@client.txt`, созданный с клиента.

![Сервер: ~/common — появился kolade@client.txt, созданный с клиента](Screenshot_16.png){ #fig:016 width=80% }

## Скрипты Vagrant для NFS

На скриншоте 17 показан скрипт `nfs.sh` для сервера (`/vagrant/provision/server/nfs.sh`).

![Скрипт nfs.sh для сервера (provision/server/nfs.sh)](Screenshot_17.png){ #fig:017 width=80% }

На скриншоте 18 показан скрипт `nfs.sh` для клиента (`/vagrant/provision/client/nfs.sh`).

![Скрипт nfs.sh для клиента (provision/client/nfs.sh)](Screenshot_18.png){ #fig:018 width=80% }

Содержимое скрипта `nfs.sh` для сервера:

```bash
#!/bin/bash
echo "Provisioning script $0"
echo "Install needed packages"
dnf -y install nfs-utils
echo "Copy configuration files"
cp -R /vagrant/provision/server/nfs/etc/* /etc
restorecon -vR /etc
echo "Configure firewall"
firewall-cmd --add-service=nfs --permanent
firewall-cmd --add-service=mountd --add-service=rpc-bind --permanent
firewall-cmd --reload
echo "Tuning SELinux"
mkdir -p /srv/nfs
semanage fcontext -a -t nfs_t "/srv/nfs(/.*)?"
restorecon -vR /srv/nfs
echo "Mounting dirs"
mkdir -p /srv/nfs/www
mount -o bind /var/www /srv/nfs/www
echo "/var/www /srv/nfs/www none bind 0 0" >> /etc/fstab
mkdir -p /srv/nfs/home/kolade
mkdir -p -m 700 /home/kolade/common
chown kolade:kolade /home/kolade/common
mount -o bind /home/kolade/common /srv/nfs/home/kolade
echo "/home/kolade/common /srv/nfs/home/kolade none bind 0 0" >> /etc/fstab
echo "Start nfs service"
systemctl enable nfs-server
systemctl start nfs-server
systemctl restart firewalld
```

Содержимое скрипта `nfs.sh` для клиента:

```bash
#!/bin/bash
echo "Provisioning script $0"
echo "Install needed packages"
dnf -y install nfs-utils
echo "Mounting dirs"
mkdir -p /mnt/nfs
mount server.kolade.net:/srv/nfs /mnt/nfs
echo "server.kolade.net:/srv/nfs /mnt/nfs nfs _netdev 0 0" >> /etc/fstab
restorecon -vR /etc
```

В конфигурационный файл `Vagrantfile` добавлены секции провизионирования:

```ruby
server.vm.provision "server nfs",
  type: "shell",
  preserve_order: true,
  path: "provision/server/nfs.sh"

client.vm.provision "client nfs",
  type: "shell",
  preserve_order: true,
  path: "provision/client/nfs.sh"
```

# Конфигурационные файлы

## Файл /etc/exports на сервере

Итоговый файл `/etc/exports`:

```
/srv/nfs *(ro)
/srv/nfs/www 192.168.0.0/16(rw)
/srv/nfs/home/kolade 192.168.0.0/16(rw)
```

Строка 1: `/srv/nfs` — корень дерева NFS, доступен всем (`*`) только для чтения (`ro`).
Строка 2: каталог веб-сервера `/srv/nfs/www` — доступен для записи хостам сети
`192.168.0.0/16`.
Строка 3: домашний каталог пользователя `kolade` — также доступен для записи.

## Запись в /etc/fstab на сервере (NFS-часть)

```
/var/www /srv/nfs/www none bind 0 0
/home/kolade/common /srv/nfs/home/kolade none bind 0 0
```

Тип файловой системы `none`, опция `bind` означает bind-монтирование: каталог делается
доступным по другому пути без копирования данных.

## Запись в /etc/fstab на клиенте (NFS-часть)

```
server.kolade.net:/srv/nfs /mnt/nfs nfs _netdev 0 0
```

Здесь `server.kolade.net:/srv/nfs` — удалённый ресурс, `/mnt/nfs` — точка монтирования,
`nfs` — тип файловой системы, `_netdev` — монтировать после инициализации сети.

# Выводы

В ходе выполнения лабораторной работы приобретены практические навыки по установке и
настройке сервера NFSv4 в среде Vagrant (AlmaLinux).

- Установлен пакет `nfs-utils`, создано дерево NFS с корнем `/srv/nfs`, настроены права
  доступа SELinux (`semanage fcontext`, `restorecon`), запущен `nfs-server.service`.
- Межсетевой экран настроен для NFS: открыты службы `nfs`, `mountd` и `rpc-bind`. Без
  правильной настройки `firewall-cmd` клиент получает ошибку RPC при `showmount`.
- На клиенте установлен `nfs-utils`, ресурс успешно смонтирован в `/mnt/nfs`. Запись в
  `/etc/fstab` с опцией `_netdev` обеспечивает автоматическое монтирование при загрузке;
  `remote-fs.target` активен.
- Каталог веб-сервера `/var/www` подключён к дереву NFS через bind-монтирование,
  экспортирован с правами `rw` для локальной сети `192.168.0.0/16`.
- Домашний каталог пользователя `kolade` (`~/common`) подключён к дереву NFS. Проверена
  корректность прав 700: `root` на клиенте не может войти в каталог (root_squash работает
  по умолчанию). Двусторонний обмен файлами подтверждён.
- Созданы скрипты Vagrant `nfs.sh` для сервера (28 строк) и клиента (9 строк),
  автоматизирующие все этапы настройки NFS. Изменения внесены в `Vagrantfile`.

# Ответы на контрольные вопросы

**Вопрос 1. Как называется файл конфигурации, содержащий общие ресурсы NFS?**

Файл конфигурации общих ресурсов NFS называется `/etc/exports`. В этом файле
прописываются экспортируемые каталоги в формате:

```
[ файловая система ] [ кому разрешить доступ ] [ ключи опций ]
```

Например: `/srv/nfs 192.168.0.0/16(rw)` — предоставляет доступ на чтение и запись к
каталогу `/srv/nfs` всем клиентам сети `192.168.0.0/16`. После изменения файла необходимо
выполнить `exportfs -r` для применения изменений без перезапуска службы.

**Вопрос 2. Какие порты должны быть открыты для полного доступа к NFS?**

Для обеспечения полного доступа к серверу NFS в межсетевом экране должны быть открыты
следующие службы и порты:

- `nfs` — TCP/UDP порт 2049 (основной порт NFS);
- `mountd` — служба монтирования, динамический порт (регистрируется через rpcbind);
- `rpc-bind` (portmapper) — TCP/UDP порт 111 (регистрация RPC-служб);
- `nfs-server` — дополнительные порты для блокировок и статуса (`rpc.statd`, `rpc.lockd`).

В `firewall-cmd` это выполняется командами:
`firewall-cmd --add-service=nfs --add-service=mountd --add-service=rpc-bind --permanent`
и `firewall-cmd --reload`.

**Вопрос 3. Какую опцию /etc/fstab использовать для автоматического монтирования NFS при перезагрузке?**

Для автоматического монтирования NFS-ресурсов при перезагрузке следует использовать опцию
`_netdev` в файле `/etc/fstab`:

```
server.kolade.net:/srv/nfs /mnt/nfs nfs _netdev 0 0
```

Опция `_netdev` сообщает systemd и ядру, что данное устройство требует наличия сети.
Без этой опции система может попытаться смонтировать NFS-ресурс до инициализации
сетевых интерфейсов, что приведёт к ошибке загрузки.
