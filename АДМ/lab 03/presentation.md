---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка DHCP-сервера и динамических обновлений DNS
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

Приобрести практические навыки по установке и конфигурированию DHCP-сервера  
и настройке динамического обновления DNS-зон (DDNS).

# Настройка DHCP-сервера

## Базовая конфигурация Kea DHCP

![Фрагмент файла kea-dhcp4.conf](Screenshot_1.png){ #fig:001 width=80% }

## Настройка сетевых параметров и DNS-опций

![DNS-опции в конфигурации DHCP](Screenshot_2.png){ #fig:002 width=80% }

## Привязка к интерфейсу и проверка конфигурации

![Проверка конфигурационного файла](Screenshot_3.png){ #fig:003 width=80% }

## Интеграция с DNS: прямые и обратные зоны

![Прямая DNS-зона](Screenshot_4.png){ #fig:004 width=80% }

## Обратная DNS-зона

![Обратная DNS-зона](Screenshot_5.png){ #fig:005 width=80% }

## Проверка доступности DHCP-сервера по имени

![Проверка доступности имени dhcp.akabrisov.net](Screenshot_6.png){ #fig:006 width=80% }

## SELinux и межсетевой экран

![Настройка SELinux и firewall](Screenshot_7.png){ #fig:007 width=80% }

## Получение адреса клиентом по DHCP

![Содержимое файла аренды адресов DHCP](Screenshot_9.png){ #fig:008 width=80% }

## Параметры сетевых интерфейсов клиента

![Вывод ifconfig на клиенте](Screenshot_8.png){ #fig:009 width=80% }

## Создание и подключение TSIG-ключа

![Создание ключа TSIG](Screenshot_10.png){ #fig:010 width=80% }

## Разрешение обновлений DNS-зон (Bind)

![Настройка update-policy в зонах](Screenshot_11.png){ #fig:011 width=80% }

## Описание TSIG-ключа для Kea

![Файл tsig-keys.json](Screenshot_12.png){ #fig:012 width=80% }

## Настройка kea-dhcp-ddns

![Файл kea-dhcp-ddns.conf](Screenshot_13.png){ #fig:013 width=80% }

## Запуск службы DHCP-DDNS

![Запуск kea-dhcp-ddns](Screenshot_14.png){ #fig:014 width=80% }

## Включение DDNS в kea-dhcp4.conf

![Фрагмент обновлённого kea-dhcp4.conf](Screenshot_15.png){ #fig:015 width=80% }

## Запуск и проверка службы Kea DHCP

![Запуск kea-dhcp](Screenshot_16.png){ #fig:016 width=80% }

## Проверка создания DNS-записей для клиента

![Результат выполнения dig](Screenshot_17.png){ #fig:017 width=80% }

## Интерпретация результата dig

- Заголовок: успешный ответ (NOERROR)
- Сервер ответа: 192.168.1.1 (server)
- QUESTION: `client.akabrisov.net`
- ANSWER: IP-адрес клиента из DHCP
- Вывод: DHCP → Kea DDNS → Bind9 работают согласованно

## Скрипт автоматической настройки dhcp.sh

![Скрипт dhcp.sh для автоматической настройки](Screenshot_18.png){ #fig:018 width=80% }

# Выводы по проделанной работе

## Основные результаты

- Настроены сервисы:
  - Kea DHCP (выдача IP-адресов)
  - Bind9 DNS (прямые и обратные зоны)
- Реализовано динамическое обновление DNS-зон (DDNS)  
  с использованием TSIG-ключей и Kea DHCP-DDNS.
