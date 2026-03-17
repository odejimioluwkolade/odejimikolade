---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Конфигурирование HTTPS и PHP на Apache
author:
  - Одеджими Олуваколаде
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 16 марта 2026
theme: metropolis
aspectratio: 169
slide_level: 2
section-titles: true
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цели и задачи работы

## Цель лабораторной работы

Приобрести навыки конфигурирования веб-сервера Apache  
для работы по защищённому протоколу HTTPS и поддержки PHP.

# Конфигурирование HTTPS

## Создание SSL-ключа и сертификата

![Генерация сертификата](Screenshot_1.png){ width=80% }

## Настройка виртуального хоста Apache

![Конфигурация SSL-виртуального хоста](Screenshot_2.png){ width=80% }

## Перенаправление HTTP → HTTPS

![Перенаправление на HTTPS](Screenshot_3.png){ width=80% }

## Проверка работы HTTPS

![Переход на защищённое соединение](Screenshot_4.png){ width=80% }

## Просмотр сертификата в браузере

![Просмотр сертификата](Screenshot_5.png){ width=80% }

# Конфигурирование PHP

## Создание index.php

![Файл index.php](Screenshot_5.png){ width=80% }

## Проверка phpinfo()

![Вывод phpinfo()](Screenshot_6.png){ width=80% }

# Выводы по проделанной работе

## Вывод

Был настроен веб-сервер Apache для работы по протоколу HTTPS  
с использованием самоподписанного сертификата.  
Добавлена поддержка PHP и выполнена проверка его работы.  
Обновлены файлы внутреннего окружения Vagrant  
для сохранения всех выполненных изменений.
