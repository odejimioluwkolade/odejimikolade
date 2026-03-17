---
## Front matter
title: "Отчёт по лабораторной работе 4"
subtitle: "Подготовка экспериментального стенда GNS3"
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

Установка и настройка GNS3 и сопутствующего программного обеспечения.

# Выполнение

## Развёртывание и первичная настройка GNS3

1. На локальном компьютере под управлением ОС Windows выполнена установка программного комплекса **GNS3-all-in-one** и виртуальной машины **GNS3 VM**.  
   После завершения установки произведён первый запуск GNS3 и начальная настройка с использованием мастера настройки.

2. В процессе настройки GNS3 выполнено подключение к удалённому контроллеру **GNS3 VM**.  
   В окне мастера настройки указаны протокол HTTP, IP-адрес контроллера `192.168.133.145`, порт `80/TCP`, а также учётные данные администратора.

   ![Настройка подключения к удалённому GNS3 controller](Screenshot_1.png){ #fig:001 width=80% }

3. После завершения настройки проверена доступность контроллера и корректность взаимодействия GNS3 с виртуальной машиной. Ошибок подключения выявлено не было, что подтверждает успешный запуск GNS3 VM.

## Импорт образа маршрутизатора FRR

4. Через менеджер устройств GNS3 выполнен выбор и добавление сетевого устройства **FRR (FRRouting Project)**, работающего под эмулятором QEMU.

   ![Выбор образа FRR в списке доступных устройств](Screenshot_2.png){ #fig:002 width=80% }

5. В процессе установки выбран образ **FRR version 8.2.2**, имеющий статус *Ready to install*. Остальные версии отмечены как отсутствующие и не использовались.

   ![Выбор версии FRR для установки](Screenshot_3.png){ #fig:003 width=80% }

6. После установки отображено информационное окно с инструкциями по использованию FRR, включая данные для входа и порядок возврата в CLI маршрутизатора.

   ![Информация об использовании FRR](Screenshot_4.png){ #fig:004 width=80% }

7. Для проверки корректности установки выполнен запуск экземпляра маршрутизатора FRR и подключение к его консоли.  
   В процессе загрузки зафиксирован успешный старт системных служб и переход в командную строку FRR, что подтверждает работоспособность образа.

   ![Запуск и загрузка маршрутизатора FRR](Screenshot_7.png){ #fig:005 width=80% }

## Импорт образа маршрутизатора VyOS

8. Аналогичным образом выполнен импорт маршрутизатора **VyOS** через менеджер устройств GNS3.  
   Для установки выбран образ **VyOS version 1.3.3 (qemu)**, имеющий статус *Ready to install* и обнаруженный локально.

   ![Выбор версии VyOS для установки](Screenshot_5.png){ #fig:006 width=80% }

9. После завершения установки отображено окно с инструкциями по использованию VyOS, включая стандартные учётные данные `vyos/vyos` и сведения о порядке загрузки образа.

   ![Информация об использовании VyOS](Screenshot_6.png){ #fig:007 width=80% }

10. Для проверки корректности импорта выполнен запуск виртуального маршрутизатора VyOS и подключение к его консоли.  
    В ходе загрузки успешно определена виртуализация KVM, инициализированы системные службы и отображено приглашение входа в систему VyOS, что подтверждает успешную установку и готовность маршрутизатора к работе.

    ![Запуск и загрузка маршрутизатора VyOS](Screenshot_8.png){ #fig:008 width=80% }


# Вывод

В ходе выполнения работы выполнена установка и первичная настройка программного комплекса GNS3 и виртуальной машины GNS3 VM. Произведён импорт и проверка работоспособности образов маршрутизаторов FRR и VyOS. Успешный запуск устройств и корректная инициализация их системных служб подтверждают готовность лабораторного стенда к дальнейшему моделированию и исследованию сетевых конфигураций.
