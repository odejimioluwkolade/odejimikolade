---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Лабораторная работа №10 — Расширенные настройки SMTP-сервера
author:
  - Одеджими Олуваколаде
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 16 марта 2026

## i18n babel
babel-lang: russian
babel-otherlangs: english

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

Приобрести практические навыки конфигурирования SMTP-сервера  
в части настройки аутентификации и защищённой передачи данных.

## Задачи

- Настроить доставку почты через LMTP (Postfix → Dovecot)
- Включить SMTP-аутентификацию (SASL через Dovecot)
- Настроить SMTP over TLS (STARTTLS) и порт submission 587
- Проверить работу через консольные тесты и почтовый клиент
- Зафиксировать изменения в provisioning-окружении Vagrant

# Выполнение лабораторной работы

## Добавление LMTP в список протоколов

![Добавление LMTP в dovecot.conf](Screenshot_1.png){ width=80% }

## Настройка сервиса LMTP и unix-сокета

![Сервис LMTP в 10-master.conf](Screenshot_2.png){ width=80% }

## Формат имени пользователя для аутентификации

![auth_username_format в 10-auth.conf](Screenshot_3.png){ width=80% }

## Доставка сообщения: анализ логов

![Логи доставки через LMTP](Screenshot_4.png){ width=80% }

## Проверка почтового ящика Maildir

![Письмо доставлено в Maildir](Screenshot_5.png){ width=80% }

## Служба auth в Dovecot

![service auth в 10-master.conf](Screenshot_6.png){ width=80% }

## Настройка SASL в Postfix

![Postfix: параметры SASL](Screenshot_7.png){ width=80% }

## Временная конфигурация для теста SMTP AUTH

![master.cf для тестирования SMTP AUTH](Screenshot_8.png){ width=80% }

## Проверка SMTP AUTH

![Успешная SMTP-аутентификация](Screenshot_9.png){ width=80% }

## Подключение TLS (сертификат Dovecot)

![TLS-параметры Postfix](Screenshot_10.png){ width=80% }

## Порт submission (587) и обязательный TLS

![Сервис submission в master.cf](Screenshot_11.png){ width=80% }

## Проверка STARTTLS и аутентификации на 587

![Проверка SMTP over TLS](Screenshot_12.png){ width=80% }

## Настройка Evolution

![Параметры SMTP в Evolution](Screenshot_13.png){ width=80% }

## Результат проверки

![Сообщения в Evolution](Screenshot_14.png){ width=80% }

## Логи сервера при работе TLS и AUTH

![Фрагмент maillog: TLS + SASL + LMTP](Screenshot_15.png){ width=80% }

# Итоги работы

## Вывод

- Настроена доставка почты через LMTP (Postfix → Dovecot)
- Реализована SMTP-аутентификация пользователей (SASL через Dovecot)
- Включён SMTP over TLS и настроен submission 587 с обязательным шифрованием
- Выполнено тестирование через консоль и Evolution
- Конфигурация перенесена в provisioning, обеспечено воспроизводимое развёртывание
