---
## Front matter
lang: ru-RU
title: Методы кодирования и модуляция сигналов
subtitle: Лабораторная работа №1
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
toc-title: Содержание
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Введение

## Цель и задачи

- Изучить методы построения сигналов в Octave  
- Определить спектры сигналов и их сумм (БПФ)  
- Продемонстрировать амплитудную модуляцию  
- Реализовать и сравнить методы кодирования:  
  Unipolar, AMI, NRZ, RZ, Manchester, Diff. Manchester  
- Проверить свойство самосинхронизации  

# Построение графиков

## Суммарные гармоники

![Графики y1 и y2](plot-sin-cos.png){ width=78% }

# Ряд Фурье

## Приближение меандра
- Нечётные гармоники  
- Амплитуда ∝ 1/n  
- Эффект Гиббса на фронтах  

![Меандр (cos-разложение)](meandr.png){ width=78% }

# Спектральный анализ

## Сумма синусоид (10 Гц и 40 Гц)
- Пики на частотах составляющих  
- Нормировка спектра  
- Алиасинг при fd < 80 Гц  

![Спектр суммы сигналов](spectr_sum/spectre/spectre_sum.png){ width=78% }

# Амплитудная модуляция

## Сигнал и спектр АМ
- Несущая: 50 Гц  
- Модуляция: 5 Гц  
- Боковые полосы вокруг несущей  

![АМ сигнал](modulation/signal/am.png){ width=46% }
![АМ спектр](modulation/spectre/am.png){ width=46% }

# Кодирование сигналов

## Примеры форм
Сравнение линий по переходам и спектральным свойствам.  

![Unipolar](coding/signal/unipolar.png){ width=48% }
![Manchester](coding/signal/manchester.png){ width=48% }

# Самосинхронизация

## Иллюстрация
Коды с переходом в середине бита обеспечивают синхронизацию при длинных сериях.  

![Manchester sync](coding/sync/manchester.png){ width=60% }

# Итоги

## Выводы
- БПФ подтверждает частотный состав сигналов  
- АМ демонстрирует огибающую и боковые полосы  
- Манчестер и дифф. Манчестер — самосинхронизируемые  
- Unipolar и NRZ хуже при длинных сериях  
- Практика в Octave закрепила навыки анализа сигналов  
