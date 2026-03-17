---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка firewalld, Port Forwarding и Masquerading в Linux
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

Получить практические навыки настройки межсетевого экрана firewalld  
в части создания служб, перенаправления портов и активации Masquerading.

---

# Выполнение работы

## Копирование и просмотр стандартной службы SSH

![Просмотр оригинального файла ssh-custom.xml](Screenshot_1.png){ #fig:001 width=80% }

## Внесение изменений в файл службы

![Редактирование службы ssh-custom.xml](Screenshot_2.png){ #fig:002 width=80% }

## Проверка списка служб и добавление новой

![Добавление службы и проверка](Screenshot_3.png){ #fig:003 width=80% }

## Port Forwarding 2022 → 22

![Проверка нового порта SSH](Screenshot_4.png){ #fig:004 width=80% }

## Port Forwarding 2022 → 22

![Проверка нового порта SSH](Screenshot_5.png){ #fig:005 width=80% }

## Проверка параметров ядра

![Проверка sysctl параметра ip_forward](Screenshot_6.png){ #fig:006 width=80% }

## Включение forwarding и masquerading

![Настройка masquerading](Screenshot_7.png){ #fig:007 width=80% }

## Проверка доступа в Интернет с клиента

![Ping с клиента](Screenshot_8.png){ #fig:008 width=80% }

## Создание provisioning-скрипта firewall.sh

![Скрипт firewall.sh](Screenshot_9.png){ #fig:009 width=80% }

# Выводы по работе

## Вывод

- Создана пользовательская служба firewalld на базе SSH.  
- Настроено перенаправление порта 2022 → 22.  
- Включены IPv4 forwarding и masquerading.  
- Проверен доступ клиента во внешнюю сеть.  
- Подготовлены provisioning-файлы для автоматизации конфигурации Firewalld.  
