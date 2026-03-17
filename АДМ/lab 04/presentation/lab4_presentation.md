---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка HTTP-сервера Apache и виртуального хостинга
author:
  - Одеджими Олуваколаде
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 16 марта 2026

theme: metropolis
slide_level: 2
aspectratio: 169
section-titles: true
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Основная цель

Получить практические навыки установки, настройки и проверки работы HTTP-сервера Apache  
с поддержкой виртуального хостинга и интеграцией с DNS. :contentReference[oaicite:0]{index=0}

# Настройка Apache

## Тестирование клиентом

![Тестовая страница HTTP](Screenshot_2.png){ width=70% }

## Прямая и обратная зоны

![Файл прямой зоны](Screenshot_4.png){ width=70% }

## Прямая и обратная зоны

![Файл обратной зоны](Screenshot_5.png){ width=70% }

## Файл server.akabrikosov.net.conf

![server.akabrikosov.net.conf](Screenshot_6.png){ width=75% }

## Файл www.akabrikosov.net.conf

![www.akabrikosov.net.conf](Screenshot_7.png){ width=75% }

## Тестовые страницы

![Создание index.html](Screenshot_8.png){ width=75% }

## server.akabrikosov.net  
![server](Screenshot_9.png){ width=65% }

## www.akabrikosov.net  
![www](Screenshot_10.png){ width=65% }

# Вывод

## Итоги работы

- Установлен и настроен HTTP-сервер Apache  
- Реализован виртуальный хостинг для двух доменных имён  
- Настроены DNS-зоны и интеграция с веб-сервисом  
- Проверена корректная работа обоих сайтов  
- Конфигурации перенесены в окружение Vagrant для автоматизации развертывания :contentReference[oaicite:1]{index=1}
