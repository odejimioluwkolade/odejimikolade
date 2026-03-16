---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Базовая защита от атак типа «brute force» с использованием Fail2ban
author:
  - Абрикосов Артём
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 10 января 2026

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

Получить практические навыки настройки и использования Fail2ban  
для базовой защиты сервера от атак типа «brute force»  
на примере служб SSH, HTTP и почтовых сервисов.

# Выполнение лабораторной работы

## Установка и запуск Fail2ban

![Просмотр журнала Fail2ban после запуска сервиса](Screenshot_1.png){ #fig:001 width=80% }

## Настройка защиты SSH

![Настройка защиты SSH](Screenshot_2.png){ #fig:002 width=80% }

## Проверка запуска jail SSH

![Журнал Fail2ban после включения защиты SSH](Screenshot_3.png){ #fig:003 width=80% }

## Настройка защиты веб-служб (Apache)

![Включение защиты HTTP-сервисов](Screenshot_4.png){ #fig:004 width=80% }

## Проверка защиты HTTP

![Журнал Fail2ban после включения защиты HTTP](Screenshot_5.png){ #fig:005 width=80% }

## Настройка защиты почтовых сервисов

![Включение защиты почтовых сервисов](Screenshot_6.png){ #fig:006 width=80% }

## Проверка защиты почты

![Журнал Fail2ban после включения защиты почты](Screenshot_7.png){ #fig:007 width=80% }

## Проверка статуса Fail2ban

![Проверка статуса Fail2ban](Screenshot_8.png){ #fig:008 width=80% }

## Блокировка IP-адреса клиента

![Блокировка IP-адреса клиента](Screenshot_9.png){ #fig:009 width=80% }

## Разблокировка и исключение IP-адреса

![Настройка ignoreip](Screenshot_10.png){ #fig:010 width=80% }

## Проверка ignoreip

![Журнал Fail2ban с ignoreip](Screenshot_11.png){ #fig:011 width=80% }

## Подготовка provisioning-конфигурации

![Скрипт protect.sh](Screenshot_12.png){ #fig:012 width=80% }

# Выводы по проделанной работе

## Вывод

В ходе лабораторной работы была реализована базовая защита сервера  
от атак типа «brute force» с использованием Fail2ban.  
Настроена защита SSH, веб-служб и почтовых сервисов,  
проверены механизмы блокировки, разблокировки и исключений IP-адресов.  
Дополнительно выполнена автоматизация настройки Fail2ban  
в процессе развертывания виртуальной машины с помощью Vagrant.
