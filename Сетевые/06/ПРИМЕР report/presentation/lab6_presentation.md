---
## Front matter
lang: ru-RU
title: Отчёт по лабораторной работе 6
subtitle: Адресация IPv4 и IPv6. Двойной стек
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

Изучение принципов распределения и настройки адресного пространства на устройствах сети.

# Выполнение лабораторной работы

## Топология лабораторного стенда в GNS3

![Топология лабораторного стенда в GNS3](Screenshot_1.png){ #fig:001 width=80% }

## Настройка IPv4-адресации на PC1-akabrikosov

![Настройка IPv4-адресации на PC1-akabrikosov](Screenshot_2.png){ #fig:002 width=80% }

## Настройка IPv4-адресации на PC2-akabrikosov

![Настройка IPv4-адресации на PC2-akabrikosov](Screenshot_3.png){ #fig:003 width=80% }

## Настройка IPv4-адресации на Server-akabrikosov

![Настройка IPv4-адресации на Server-akabrikosov](Screenshot_4.png){ #fig:004 width=80% }

## Настройка интерфейсов маршрутизатора msk-akabrikosov-gw-01

![Настройка интерфейсов маршрутизатора msk-akabrikosov-gw-01](Screenshot_5.png){ #fig:005 width=80% }

## Проверка состояния интерфейсов маршрутизатора

![Проверка состояния интерфейсов маршрутизатора](Screenshot_6.png){ #fig:006 width=80% }

## Проверка связности с PC1-akabrikosov

![Проверка связности с PC1-akabrikosov](Screenshot_7.png){ #fig:007 width=80% }

## Проверка связности с PC2-akabrikosov

![Проверка связности с PC2-akabrikosov](Screenshot_8.png){ #fig:008 width=80% }

## Настройка IPv6-адресации на PC3-akabrikosov

![Настройка IPv6-адресации на PC3-akabrikosov](Screenshot_9.png){ #fig:009 width=80% }

## Настройка IPv6-адресации на PC4-akabrikosov

![Настройка IPv6-адресации на PC4-akabrikosov](Screenshot_10.png){ #fig:010 width=80% }

## Проверка IPv4- и IPv6-адресации на Server-akabrikosov

![Проверка IPv4- и IPv6-адресации на Server-akabrikosov](Screenshot_11.png){ #fig:011 width=80% }

## Настройка IPv6-интерфейсов маршрутизатора VyOS

![Настройка IPv6-интерфейсов маршрутизатора VyOS](Screenshot_12.png){ #fig:012 width=80% }

## Проверка интерфейсов маршрутизатора VyOS

![Проверка интерфейсов маршрутизатора VyOS](Screenshot_13.png){ #fig:013 width=80% }

## Проверка IPv6-связности с PC3-akabrikosov

![Проверка IPv6-связности с PC3-akabrikosov](Screenshot_14.png){ #fig:014 width=80% }

## Проверка IPv6-связности с PC4-akabrikosov

![Проверка IPv6-связности с PC4-akabrikosov](Screenshot_15.png){ #fig:015 width=80% }

## Проверка изоляции IPv4 и IPv6 подсетей

![Проверка изоляции IPv4 и IPv6 подсетей](Screenshot_16.png){ #fig:016 width=80% }

## Захват ARP-трафика на интерфейсе сервера

![Захват ARP-трафика на интерфейсе сервера](Screenshot_17.png){ #fig:017 width=80% }

## Захват ICMP-трафика IPv4

![Захват ICMP-трафика IPv4](Screenshot_18.png){ #fig:018 width=80% }

## Захват ICMPv6-трафика

![Захват ICMPv6-трафика](Screenshot_19.png){ #fig:019 width=80% }

## Топология сети с двумя локальными подсетями

![Топология сети с двумя локальными подсетями](Screenshot_20.png){ #fig:020 width=80% }

## Проверка IPv4 и IPv6 адресации на PC1-akabrikosov

![Проверка IPv4 и IPv6 адресации на PC1-akabrikosov](Screenshot_21.png){ #fig:021 width=80% }

## Проверка IPv4 и IPv6 адресации на PC2-akabrikosov

![Проверка IPv4 и IPv6 адресации на PC2-akabrikosov](Screenshot_22.png){ #fig:022 width=80% }

## Проверка настроенной адресации интерфейсов VyOS

![Проверка настроенной адресации интерфейсов VyOS](Screenshot_23.png){ #fig:023 width=80% }

## Проверка связности ping и trace между PC1 и PC2

![Проверка связности ping и trace между PC1 и PC2](Screenshot_24.png){ #fig:024 width=80% }


# Выводы по проделанной работе

## Вывод

В ходе работы:
- выполнено разбиение IPv4 и IPv6 адресных пространств на подсети;
- настроен двойной стек IPv4/IPv6 в GNS3;
- подтверждена связность внутри соответствующих подсетей;
- подтверждена изоляция IPv4 и IPv6 сегментов (кроме Dual Stack сервера);
- выполнен анализ ARP, ICMP, ICMPv6 в захваченном трафике.