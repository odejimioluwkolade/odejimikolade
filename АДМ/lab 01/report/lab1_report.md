---
## Front matter
title: "Отчёт по лабораторной работе 1"
subtitle: " Подготовка лабораторного стенда"
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

Целью данной работы является приобретение практических навыков установки Rocky Linux на виртуальную машину с помощью инструмента Vagrant.

# Выполнение

## Развёртывание лабораторного стенда на ОС Windows

1. Подготовлена рабочая среда на локальном компьютере под управлением Windows.  
   Установлены и настроены VirtualBox и Vagrant. После установки система была перезагружена.

2. В рабочем каталоге проекта созданы необходимые файлы для автоматического развертывания — 01-user.sh, 01-hostname.sh и Vagrantfile.

   ![Скрипт 01-user.sh](Screenshot_1.png){ #fig:001 width=80% }

   ![Скрипт 01-hostname.sh](Screenshot_2.png){ #fig:002 width=80% }

   ![Фрагмент Vagrantfile с описанием provision](Screenshot_3.png){ #fig:003 width=80% }

3. Выполнена регистрация box-файла в Vagrant с помощью команды:

   vagrant box add rockylinux10 vagrant-virtualbox-rockylinux10-x86_64.box

   ![Добавление box-файла в Vagrant](Screenshot_4.png){ #fig:004 width=80% }

4. Выполнен запуск виртуальной машины Server:

   vagrant up server

   ![Запуск виртуальной машины server](Screenshot_5.png){ #fig:005 width=80% }

5. После успешного запуска на экране входа в систему появились пользователи vagrant и созданный скриптом akabrikosov.

   ![Окно входа в систему Rocky Linux](Screenshot_6.png){ #fig:006 width=80% }

6. Проверено, что при входе в систему отображается корректное имя хоста server.akabrikosov.net.

   ![Проверка имени хоста в терминале](Screenshot_7.png){ #fig:007 width=80% }

7. Подключение к серверу через консоль выполнено командой:

   vagrant ssh server

   После ввода пароля осуществлён вход и произведена проверка возможности переключения на пользователя akabrikosov:

   su - akabrikosov

   Затем выполнен выход из системы.

   ![Подключение к серверу через SSH](Screenshot_8.png){ #fig:008 width=80% }

# Вывод

В результате работы был развернут лабораторный стенд на базе Vagrant и VirtualBox в среде Windows.  
Созданы и протестированы сценарии автоматического развертывания виртуальной машины с использованием скриптов 01-user.sh и 01-hostname.sh.  
Проверена корректность работы созданного пользователя, hostname и подключения через SSH.  

# Контрольные вопросы

1. Для чего предназначен Vagrant  
   Vagrant предназначен для автоматизации развертывания и управления виртуальными машинами.  
   Он упрощает настройку окружений для разработки и тестирования, обеспечивая единообразие конфигураций.

2. Что такое box-файл? В чём назначение Vagrantfile  
   Box-файл — это шаблон готовой виртуальной машины, содержащий операционную систему и базовые настройки.  
   Vagrantfile — это конфигурационный файл, описывающий параметры виртуальных машин: используемый box, настройки сети, ресурсы, а также сценарии инициализации (provision).

3. Приведите описание и примеры вызова основных команд Vagrant  
   - `vagrant init` — создаёт новый Vagrantfile в текущем каталоге.  
   - `vagrant box add <имя> <путь к box>` — добавляет образ виртуальной машины в локальное хранилище.  
   - `vagrant up` — запускает и создаёт виртуальную машину.  
   - `vagrant ssh` — подключается к запущенной машине по SSH.  
   - `vagrant halt` — корректно останавливает виртуальную машину.  
   - `vagrant destroy` — полностью удаляет виртуальную машину.  
   - `vagrant status` — показывает текущее состояние всех машин проекта.

4. Дайте построчные пояснения содержания файлов vagrant-rocky.pkr.hcl, ks.cfg, Vagrantfile, Makefile  
   - **vagrant-rocky.pkr.hcl** — конфигурация для утилиты Packer, определяющая процесс сборки box-файла (указывается источник ISO, параметры установки и скрипты автоматизации).  
   - **ks.cfg** — файл автоматической установки системы (kickstart), в котором задаются параметры установки ОС: язык, разделы диска, учетные данные, пакеты.  
   - **Vagrantfile** — основной сценарий Vagrant, описывающий создание виртуальных машин (server, client), их параметры сети, провижнинг и запуск вспомогательных скриптов.  
   - **Makefile** — содержит набор команд для автоматизации часто выполняемых действий, таких как сборка, запуск или уничтожение виртуальных машин.
