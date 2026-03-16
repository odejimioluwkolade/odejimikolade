---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Установка и настройка MariaDB
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

# Цели и задачи работы

## Цель лабораторной работы

Овладеть практическими навыками установки, конфигурирования  
и администрирования системы управления базами данных MariaDB.

# Выполнение лабораторной работы

## Установка пакетов MariaDB

![Установка пакетов MariaDB](Screenshot_1.png){ #fig:001 width=80% }

## Настройка безопасности

![mysql_secure_installation](Screenshot_2.png){ #fig:003 width=80% }

## Список клиентских команд

![Команды клиента MySQL](Screenshot_3.png){ #fig:004 width=80% }

## Просмотр доступных баз данных

![SHOW DATABASES](Screenshot_4.png){ #fig:005 width=60% }

## Статус MariaDB до изменения кодировки

![Статус MariaDB — до настройки UTF-8](Screenshot_5.png){ #fig:006 width=80% }

## Создание файла utf8.cnf

![Файл utf8.cnf](Screenshot_6.png){ #fig:007 width=70% }

## Статус MariaDB после настройки UTF-8

![Статус MariaDB — после настройки UTF-8](Screenshot_7.png){ #fig:008 width=80% }

## Создание таблицы и добавление данных

![Таблица city](Screenshot_8.png){ #fig:009 width=80% }

## Создание пользователя и назначение привилегий

![Права пользователя](Screenshot_9.png){ #fig:010 width=80% }

## Просмотр структуры таблицы

![DESCRIBE city](Screenshot_10.png){ #fig:011 width=60% }

## Просмотр списка таблиц

![mysqlshow addressbook](Screenshot_11.png){ #fig:012 width=80% }

## Создание резервных копий

![Резервные копии БД](Screenshot_12.png){ #fig:013 width=80% }

## Скрипт автоматической настройки MariaDB

![Скрипт mysql.sh](Screenshot_13.png){ #fig:015 width=80% }


# Выводы

## Итоги выполненной лабораторной работы

В результате работы была проведена полная установка и настройка MariaDB:  
- Установлены серверные и клиентские пакеты.  
- Настроена система безопасности MariaDB.  
- Созданы база данных и таблицы, добавлены данные.  
- Настроены пользователи и привилегии.  
- Созданы резервные копии и выполнено восстановление базы.  
- Реализована автоматизация развёртывания с помощью скрипта mysql.sh.  

Получены навыки администрирования серверов баз данных и управления окружением MariaDB.

