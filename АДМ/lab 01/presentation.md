---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Развёртывание лабораторного стенда с использованием Vagrant
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

Овладеть практическими навыками установки операционной системы Rocky Linux  
на виртуальную машину с помощью инструмента Vagrant и автоматизации процесса развёртывания.

# Выполнение лабораторной работы

## Подготовка среды

   ![Подготовка среды VirtualBox и Vagrant](Screenshot_1.png){ #fig:001 width=80% }

## Создание файлов проекта

   ![Скрипт 01-user.sh](Screenshot_2.png){ #fig:002 width=80% }

## Создание файлов проекта

   ![Скрипт 01-hostname.sh](Screenshot_3.png){ #fig:003 width=80% }

## Конфигурация Vagrantfile

   ![Файл Vagrantfile с описанием provision](Screenshot_4.png){ #fig:004 width=80% }

## Регистрация box-файла в Vagrant

   ![Добавление box-файла в систему Vagrant](Screenshot_5.png){ #fig:005 width=80% }

## Запуск виртуальной машины Server

   ![Запуск виртуальной машины server](Screenshot_6.png){ #fig:006 width=80% }

## Проверка входа в систему

   ![Окно входа в систему Rocky Linux](Screenshot_7.png){ #fig:007 width=80% }

## Проверка имени хоста

   ![Отображение hostname server.akabrikosov.net](Screenshot_8.png){ #fig:008 width=80% }

# Выводы по проделанной работе

## Вывод

В результате работы был развернут лабораторный стенд на базе VirtualBox и Vagrant.  
Были созданы и протестированы сценарии автоматического развёртывания виртуальной машины  
с использованием скриптов 01-user.sh и 01-hostname.sh.  
Проверена корректность настроек пользователя, имени хоста и подключения по SSH.  
