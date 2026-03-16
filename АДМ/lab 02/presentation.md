---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка и анализ работы DNS-сервера
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

Приобрести практические навыки установки и конфигурирования DNS-сервера  
и закрепить понимание принципов работы системы доменных имён.

# Установка и настройка DNS-сервера

## Установка пакетов и проверка работы DNS

![Результат выполнения команды dig www.yandex.ru](Screenshot_1.png){ #fig:001 width=80% }

## Анализ конфигурационных файлов DNS

![Просмотр содержимого файлов resolv.conf и named.conf](Screenshot_2.png){ #fig:002 width=80% }

## Анализ конфигурационных файлов DNS

![Содержимое файла named.ca](Screenshot_3.png){ #fig:003 width=80% }

## Анализ конфигурационных файлов DNS

![Содержимое файлов named.localhost и named.loopback](Screenshot_4.png){ #fig:004 width=80% }

## Проверка работы DNS через localhost

![Запрос к DNS через localhost](Screenshot_5.png){ #fig:005 width=80% }

# Конфигурирование первичного DNS-сервера

## Добавление файла зоны и настройка конфигурации

![Добавление файла описания зоны в named.conf](Screenshot_9.png){ #fig:009 width=80% }

## Создание прямой и обратной зон

![Описание прямой и обратной зон в файле akabrikosov.net](Screenshot_10.png){ #fig:010 width=80% }

## Создание файла прямой зоны

![Файл прямой зоны akabrikosov.net](Screenshot_11.png){ #fig:011 width=80% }

## Создание файла обратной зоны

![Файл обратной зоны 192.168.1](Screenshot_12.png){ #fig:012 width=80% }

## Настройка прав и контекстов SELinux

![Исправление прав доступа и контекстов SELinux](Screenshot_13.png){ #fig:013 width=80% }

# Анализ работы DNS-сервера

## Проверка через dig

![Результат запроса dig ns.akabrikosov.net](Screenshot_14.png){ #fig:014 width=80% }

## Проверка через host

![Проверка зоны командой host -l и host -a](Screenshot_15.png){ #fig:015 width=80% }

## Проверка прямой и обратной записи

![Проверка прямой и обратной записи](Screenshot_16.png){ #fig:016 width=80% }

# Внесение изменений в окружение

## Копирование конфигураций DNS в среду Vagrant

![Создание структуры каталогов и копирование файлов DNS](Screenshot_17.png){ #fig:017 width=80% }

## Автоматизация конфигурации через dns.sh

![Содержимое скрипта dns.sh](Screenshot_18.png){ #fig:018 width=80% }

# Выводы по проделанной работе

## Вывод

В результате лабораторной работы был установлен и настроен DNS-сервер с использованием **bind**.  
Созданы прямые и обратные зоны домена **akabrikosov.net**, внесены необходимые записи.  
Настроены права доступа и контексты SELinux, реализована автоматизация конфигурации через скрипт **dns.sh**.  
Работоспособность DNS-сервера подтверждена тестами утилит **dig** и **host**.
