---
## Front matter
title: "Отчёт по лабораторной работе 7"
subtitle: "Расширенные настройки межсетевого экрана"
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

Получить навыки настройки межсетевого экрана в Linux в части переадресации портов и настройки Masquerading.

# Выполнение

## Создание пользовательской службы firewalld

### Копирование и просмотр базового описания службы SSH

Для создания собственной службы firewalld на основе системного файла был скопирован оригинальный файл описания службы SSH в каталог `/etc/firewalld/services/`.

После этого файл был просмотрен для анализа структуры и содержания.

![Просмотр оригинального файла ssh-custom.xml](Screenshot_1.png){ #fig:001 width=80% }

#### Построчное описание синтаксиса файла

- `<?xml version="1.0" encoding="utf-8"?>` — заголовок XML-документа, указывающий версию и кодировку.
- `<service>` — основной контейнер, описывающий новую службу.
- `<short>SSH</short>` — краткое название службы, отображаемое в списках.
- `<description>...</description>` — расширенное текстовое описание назначения службы.
- `<port protocol="tcp" port="22"/>` — параметр, определяющий протокол и номер порта.
- `</service>` — закрывающий тег описания службы.

### Редактирование файла службы

В исходный файл были внесены изменения:

- порт изменён с 22 на 2022;
- описание дополнено, чтобы отразить модификацию;
- структура XML сохранена без изменений.

![Редактирование службы ssh-custom.xml](Screenshot_2.png){ #fig:002 width=80% }


## Регистрация новой службы в firewalld

### Проверка списка доступных служб

После редактирования был просмотрен список доступных в системе FirewallD служб.  
На данном этапе созданная служба ещё не отображалась в списке.

Пользовательская служба была добавлена в активный набор правил, затем — в постоянную конфигурацию.  
После перезагрузки правил межсетевого экрана служба появилась в списке активных.

![Добавление службы и проверка](Screenshot_3.png){ #fig:003 width=80% }

## Перенаправление порта 2022 → 22

На сервере было настроено перенаправление трафика с внешнего порта 2022 на внутренний 22, что позволяет подключаться по SSH через новый порт.

Проверка проводилась с клиентской машины: подключение по SSH через порт 2022 прошло успешно.

![Проверка нового порта SSH](Screenshot_4.png){ #fig:004 width=80% }

![Проверка нового порта SSH](Screenshot_5.png){ #fig:005 width=80% }

## Port Forwarding и Masquerading

### Проверка параметров ядра

Параметр `net.ipv4.ip_forward` по умолчанию был отключён в системе.  
Это требовало включения пересылки IPv4-пакетов.

![Проверка sysctl параметра ip_forward](Screenshot_6.png){ #fig:006 width=80% }

### Включение пересылки и masquerading

После создания конфигурационного файла с параметром включения пересылки и применения его изменений в системе был активирован маскарадинг в зоне `public`.

![Настройка masquerading](Screenshot_7.png){ #fig:007 width=80% }

### Проверка выхода в Интернет с клиента

Клиентская машина успешно получила доступ во внешнюю сеть.

![Ping с клиента](Screenshot_8.png){ #fig:008 width=80% }

## Внесение настроек во внутреннее окружение виртуальной машины

### Копирование конфигураций FirewallD

В каталог `/vagrant/provision/server/firewall/` были созданы необходимые подкаталоги и помещены:

- файл службы `ssh-custom.xml`;
- конфигурация `90-forward.conf`.

### Создание скрипта автоматической настройки окружения

В каталоге `/vagrant/provision/server` был создан исполняемый файл `firewall.sh`, содержащий команды:

- копирование конфигураций в систему;
- добавление пользовательской службы;
- настройку перенаправления порта;
- включение masquerading;
- перезагрузку конфигурации firewalld;
- восстановление контекстов SELinux.

![Скрипт firewall.sh](Screenshot_9.png){ #fig:009 width=80% }


# Вывод

В ходе работы была создана собственная служба firewalld на основе системного описания SSH.  
Произведена корректировка конфигурационного файла службы, её регистрация в системе и активация.  
Настроено перенаправление порта 2022 на 22, обеспечена работа SSH через пользовательский порт.  
Включены пересылка IPv4-пакетов и маскарадинг, что позволило организовать доступ клиента во внешнюю сеть.  
Созданы каталоги и файлы внутреннего окружения Vagrant, подготовлен provisioning-скрипт для автоматизации настройки FirewallD.

# Контрольные вопросы

1. Где хранятся пользовательские файлы firewalld  
   Пользовательские файлы служб и зон хранятся в каталоге `/etc/firewalld/`.  
   Именно здесь размещаются созданные или изменённые конфигурации, которые имеют приоритет над системными.

2. Какую строку надо включить в пользовательский файл службы, чтобы указать порт TCP 2022  
   В разделе `<service>` необходимо добавить строку:  
   `<port protocol="tcp" port="2022"/>`  
   Она определяет использование TCP-порта 2022 для данной службы.

3. Какая команда позволяет перечислить все службы, доступные в настоящее время на вашем сервере  
   Для отображения доступных служб используется команда:  
   `firewall-cmd --get-services`  
   Она показывает список всех зарегистрированных служб, включая пользовательские.

4. В чем разница между трансляцией сетевых адресов (NAT) и маскарадингом (masquerading)  
   NAT — это общий механизм преобразования адресов при прохождении пакетов через маршрутизатор.  
   Маскарадинг — частный случай NAT, при котором внешний IP-адрес выбирается автоматически (обычно IP интерфейса по умолчанию).  
   Маскарадинг применяют при подключении к интернету через динамический внешний IP.

5. Какая команда разрешает входящий трафик на порт 4404 и перенаправляет его в службу ssh по IP-адресу 10.0.0.10  
   Для настройки разрешения и перенаправления используется команда:  
   `firewall-cmd --add-forward-port=port=4404:proto=tcp:toaddr=10.0.0.10:toport=22`  
   Она открывает порт 4404 и перенаправляет его на порт 22 удалённого хоста.

6. Какая команда используется для включения маскарадинга IP-пакетов для всех пакетов, выходящих в зону public  
   Для активации маскарадинга применяется команда:  
   `firewall-cmd --zone=public --add-masquerade`  
   Она разрешает замену исходного IP-адреса пакетов на адрес интерфейса зоны public.
