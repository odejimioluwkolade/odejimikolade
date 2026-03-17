---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка безопасного удалённого доступа по протоколу SSH
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

Приобрести практические навыки настройки безопасного удалённого доступа к серверу  
с помощью SSH и средств контроля (sshd_config, firewall, SELinux, ключевая аутентификация, туннели).

# Выполнение лабораторной работы

## Мониторинг событий и попытка входа root

![Мониторинг событий и попытка входа root](Screenshot_1.png){ #fig:001 width=80% }

## Ошибки аутентификации root в журнале

![Ошибки аутентификации root](Screenshot_2.png){ #fig:002 width=80% }

## Запрет удалённого входа для root

![Параметр PermitRootLogin no](Screenshot_3.png){ #fig:003 width=80% }

## Результат после запрета root

![Отказ в доступе root по SSH](Screenshot_4.png){ #fig:004 width=80% }

## Успешное подключение пользователя kolade

![Подключение пользователя kolade](Screenshot_5.png){ #fig:005 width=80% }

## Ограничение списка пользователей AllowUsers

![AllowUsers: доступ ограничен](Screenshot_6.png){ #fig:006 width=80% }

## Отказ в доступе пользователю вне AllowUsers

![Отказ пользователю вне списка AllowUsers](Screenshot_7.png){ #fig:007 width=80% }

## Разрешение доступа двум пользователям

![AllowUsers: vagrant и kolade](Screenshot_8.png){ #fig:008 width=80% }

## Настройка двух портов для SSH

![Добавление Port 2022](Screenshot_9.png){ #fig:009 width=80% }

## Ошибка bind на порт 2022

![Ошибка Bind to port 2022](Screenshot_10.png){ #fig:010 width=80% }

## Исправление SELinux и firewall для 2022

![Статус sshd после настройки SELinux и firewall](Screenshot_11.png){ #fig:011 width=80% }

## Подключение по SSH через порт 2022

![Подключение по порту 2022](Screenshot_12.png){ #fig:012 width=80% }

## Разрешение аутентификации по ключу

![PubkeyAuthentication yes](Screenshot_13.png){ #fig:013 width=80% }

## Подключение по ключу без пароля

![Подключение по ключу](Screenshot_14.png){ #fig:014 width=80% }

## SSH-туннель и перенаправление портов

![TCP-соединения и туннель](Screenshot_15.png){ #fig:015 width=80% }

## Проверка доступа к веб-странице через туннель

![Доступ к веб-странице через туннель](Screenshot_16.png){ #fig:016 width=80% }

## Запуск консольных команд через SSH

![Удалённый запуск консольных команд](Screenshot_17.png){ #fig:017 width=80% }

## Просмотр почты на сервере через SSH

![Просмотр почты на сервере](Screenshot_18.png){ #fig:018 width=80% }

## Разрешение X11 Forwarding

![X11Forwarding yes](Screenshot_19.png){ #fig:019 width=80% }

## Попытка запуска графического приложения по SSH

![Ошибка DISPLAY при X11 Forwarding](Screenshot_20.png){ #fig:020 width=80% }

# Выводы по проделанной работе

## Вывод

В ходе работы настроены механизмы безопасного удалённого доступа по SSH:  
запрет входа для root, ограничение пользователей через AllowUsers, работа через два порта с настройкой SELinux и firewall,  
аутентификация по ключу, организация SSH-туннелей и удалённый запуск приложений.  
Проверки подтвердили корректность конфигурации и соблюдение принципов безопасного доступа.
