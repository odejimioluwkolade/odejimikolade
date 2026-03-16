---
## Front matter
title: "Отчёт по лабораторной работе 15"
subtitle: "Настройка сетевого журналирования"
author: "Абрикосов Артем"

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
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a
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

Получение навыков по работе с журналами системных событий.

# Выполнение

## Настройка сервера сетевого журнала

1. На сервере выполнен переход в каталог конфигураций rsyslog и создан файл конфигурации сетевого хранения журналов `netlog-server.conf` в каталоге `/etc/rsyslog.d`.

2. В файле `/etc/rsyslog.d/netlog-server.conf` включён приём сообщений системного журнала по TCP-порту 514 с использованием модуля imtcp.

   ![Файл конфигурации netlog-server.conf](Screenshot_1.png){ #fig:009 width=80% }

3. После внесения изменений выполнен перезапуск службы rsyslog. Проверено, что служба rsyslog прослушивает TCP-порт 514 по протоколам IPv4 и IPv6.

4. На сервере настроен межсетевой экран для приёма сообщений по TCP-порту 514. Порт добавлен как во временную, так и в постоянную конфигурацию firewalld.

   ![Настройка firewalld для TCP-порта 514](Screenshot_2.png){ #fig:011 width=80% }

## Настройка клиента сетевого журнала

5. На клиентской виртуальной машине в каталоге `/etc/rsyslog.d` создан файл конфигурации сетевого журналирования `netlog-client.conf`.

6. В файле `/etc/rsyslog.d/netlog-client.conf` настроено перенаправление всех сообщений системного журнала на сервер `server.akabrikosov.net` по TCP-порту 514 с использованием надёжного соединения.

   ![Файл конфигурации netlog-client.conf](Screenshot_3.png){ #fig:012 width=80% }

7. После изменения конфигурации выполнен перезапуск службы rsyslog на клиенте.

## Просмотр и проверка журналов

8. На сервере выполнен просмотр системного журнала. В файле журнала зафиксированы сообщения как от локальных сервисов сервера, так и от клиентской виртуальной машины, что подтверждает корректную настройку сетевого журналирования.

   ![Просмотр системного журнала на сервере](Screenshot_4.png){ #fig:013 width=80% }

9. Под пользователем `akabrikosov` на сервере запущено графическое приложение `gnome-system-monitor` для контроля состояния системы и активных процессов.

   ![Запуск gnome-system-monitor](Screenshot_5.png){ #fig:014 width=80% }

10. Выполнена попытка установки просмотрщика системных журналов `lnav` на сервере. Установка завершилась ошибкой отсутствия пакета в доступных репозиториях, что зафиксировано в отчёте.

   ![Ошибка установки пакета lnav](Screenshot_6.png){ #fig:015 width=80% }

## Внесение изменений в настройки внутреннего окружения виртуальных машин

11. На виртуальной машине server в каталоге `/vagrant/provision/server` создана структура каталогов для хранения конфигураций сетевого журналирования. Файл `netlog-server.conf` скопирован в подкаталог `netlog/etc/rsyslog.d`.

12. В каталоге `/vagrant/provision/server` создан исполняемый скрипт `netlog.sh`, обеспечивающий копирование конфигурационных файлов, восстановление контекстов SELinux, настройку межсетевого экрана и перезапуск службы rsyslog.

   ![Скрипт netlog.sh для сервера](Screenshot_7.png){ #fig:016 width=80% }

13. На виртуальной машине client создан каталог `/vagrant/provision/client/netlog`, в который помещён файл `netlog-client.conf`. Также создан исполняемый скрипт `netlog.sh`, выполняющий установку необходимых пакетов, копирование конфигурационных файлов и перезапуск службы rsyslog.

   ![Скрипт netlog.sh для клиента](Screenshot_8.png){ #fig:017 width=80% }

14. Для автоматического применения настроек при загрузке виртуальных машин в файл `Vagrantfile` добавлены секции provision для сервера и клиента, обеспечивающие выполнение соответствующих скриптов `netlog.sh` в заданном порядке.


# Вывод

В ходе выполнения лабораторной работы была выполнена настройка системы централизованного сетевого журналирования на базе службы rsyslog.  
Реализован приём сообщений журнала по TCP-протоколу на сервере и настроена передача системных сообщений с клиентской виртуальной машины.  
Проверена корректность работы сетевого журналирования путём анализа записей в системных журналах сервера.  
Дополнительно выполнена автоматизация конфигурации с использованием provisioning-скриптов Vagrant, что обеспечивает воспроизводимость настроек при повторном развёртывании виртуальных машин.

# Контрольные вопросы

1. Какой модуль rsyslog вы должны использовать для приёма сообщений от journald  
   Для приёма сообщений от journald в rsyslog используется модуль **imjournal**.  
   Он обеспечивает прямое взаимодействие rsyslog с журналом systemd-journald.

2. Как называется устаревший модуль, который можно использовать для включения приёма сообщений журнала в rsyslog  
   Устаревшим модулем является **imuxsock**, который принимает сообщения через Unix-сокет `/dev/log`.

3. Чтобы убедиться, что устаревший метод приёма сообщений из journald в rsyslog не используется, какой дополнительный параметр следует использовать  
   Для этого используется параметр **OmitLocalLogging**, который предотвращает дублирование сообщений, передаваемых через сокет.

4. В каком конфигурационном файле содержатся настройки, которые позволяют вам настраивать работу журнала  
   Основные настройки работы журналирования systemd содержатся в конфигурационном файле **/etc/systemd/journald.conf**.

5. Каким параметром управляется пересылка сообщений из journald в rsyslog  
   Пересылка сообщений из journald в rsyslog управляется параметром **ForwardToSyslog**.

6. Какой модуль rsyslog вы можете использовать для включения сообщений из файла журнала, не созданного rsyslog  
   Для чтения сообщений из произвольных файлов журнала используется модуль **imfile**.

7. Какой модуль rsyslog вам нужно использовать для пересылки сообщений в базу данных MariaDB  
   Для пересылки сообщений в базу данных MariaDB применяется модуль **ommysql**.

8. Какие две строки вам нужно включить в rsyslog.conf, чтобы позволить текущему журнальному серверу получать сообщения через TCP  
   Для этого необходимо загрузить модуль **imtcp** и указать порт приёма TCP-сообщений, обычно 514.

9. Как настроить локальный брандмауэр, чтобы разрешить приём сообщений журнала через порт TCP 514  
   Для разрешения приёма сообщений журнала необходимо открыть TCP-порт 514 в правилах локального межсетевого экрана, добавив его во временную и постоянную конфигурации.
