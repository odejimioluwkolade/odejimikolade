---
## Front matter
lang: ru-RU
title: Отчёт по лабораторной работе 3
subtitle: Анализ трафика в Wireshark
author:
  - Одеджими Олуваколаде
date: 16 марта 2026

## Formatting pdf (beamer)
toc: false
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Цель лабораторной работы
Изучить в Wireshark кадры Ethernet и проанализировать PDU протоколов
транспортного и прикладного уровней стека TCP/IP.

# Анализ параметров подключения

## Получение информации о сетевом соединении (ipconfig)
По выводу ipconfig определены ключевые параметры Wi-Fi подключения:
- адаптер: **Intel(R) Wi-Fi 6E AX211 160MHz**;
- IPv4: **192.168.1.69**, маска: **255.255.255.0**;
- шлюз / DHCP / DNS: **192.168.1.1**;
- DHCP включён.

![Вывод команды ipconfig](Screenshot_1.png){ width=80% }

## MAC-адрес сетевого интерфейса
Физический адрес Wi-Fi интерфейса:
- **F8-FE-5E-6F-7B-EC**

Интерпретация структуры:
- **OUI (F8-FE-5E)** — производитель (Intel);
- **NIC part (6F-7B-EC)** — уникальная часть интерфейса.

## Тип MAC-адреса (по первому октету)
Первый октет: **F8 = 11111000₂**
- I/G (младший бит) = 0 → **unicast**
- U/L (второй младший бит) = 0 → **глобально администрируемый**

# ARP и ICMP в Wireshark

## Проверка доступности шлюза (ping 192.168.1.1)

![Проверка доступности шлюза по умолчанию](Screenshot_2.png){ width=80% }

## ICMP echo request (в Wireshark)
Фильтр: `arp or icmp`  
Характеристики кадра:
- Ethernet II, **74 байта**
- SRC MAC: **F8-FE-5E-6F-7B-EC**
- DST MAC: **C8-7F-54-78-B6-F2**

![ICMP эхо-запрос в Wireshark](Screenshot_3.png){ width=80% }

## ICMP echo reply (в Wireshark)
Характеристики кадра:
- Ethernet II, **78 байт**
- SRC MAC: **C8-7F-54-78-B6-F2**
- DST MAC: **F8-FE-5E-6F-7B-EC**

![ICMP эхо-ответ в Wireshark](Screenshot_4.png){ width=80% }

## ARP-запрос и ARP-ответ

ARP-запрос:
- Src IP: **192.168.1.69**, Dst IP: **192.168.1.1**
- Dst MAC: **broadcast**

ARP-ответ:
- Src IP: **192.168.1.1**
- Dst MAC: MAC компьютера

![ARP-запрос и ARP-ответ](Screenshot_5.png){ width=80% }

# DNS и внешний доступ

## Проверка доменного имени (ping yandex.ru)
Получение ICMP ответов подтверждает:
- корректную работу DNS-резолвинга;
- доступ в сеть через шлюз.

![Ping доменного имени yandex.ru](Screenshot_6.png){ width=80% }

## Пакеты при обращении к внешнему ресурсу
На канальном уровне трафик идёт:
- SRC MAC: MAC компьютера
- DST MAC: MAC шлюза

![Анализ пакетов при обращении к внешнему ресурсу](Screenshot_7.png){ width=80% }

## Заголовок IPv4 (пример ICMP)
Параметры заголовка IPv4:
- IHL: **20 байт**
- TTL: **128**
- Protocol: **ICMP**

![Детальный анализ ICMP пакета](Screenshot_8.png){ width=80% }

# Транспортный и прикладной уровни

## HTTP поверх TCP (порт 80): запрос
Фильтр: `http`  
Зафиксировано:
- Src IP: **192.168.1.69**
- Dst IP: **188.184.67.127**
- TCP Src port: **52480**, Dst port: **80**
- Flags: **PSH, ACK**
- Payload: **448 байт**, TCP header: **20 байт**

![HTTP-запрос и параметры TCP (порт 80)](Screenshot_9.png){ width=80% }

## HTTP поверх TCP (порт 80): ответ
Сервер отвечает по тому же соединению:
- Src port: **80** → Dst port: **52480**
- Flags: **PSH, ACK**
- Payload: **878 байт**, TCP header: **20 байт**

![HTTP-ответ и параметры TCP (порт 80)](Screenshot_10.png){ width=80% }

## DNS поверх UDP: запрос
Фильтр: `dns`  
Параметры:
- Src IP: **192.168.1.69** → Dst IP: **192.168.1.1**
- Src port: **63313** → Dst port: **53**
- UDP payload: **35 байт**
- Transaction ID: **0x1ed8**

![DNS-запрос и параметры UDP (порт 53)](Screenshot_11.png){ width=80% }

## DNS поверх UDP: ответ
Параметры ответа:
- Src IP: **192.168.1.1** → Dst IP: **192.168.1.69**
- Transaction ID: **0x1ed8** (совпадает)
- No error, Answer RRs: **2**

![DNS-ответ и параметры UDP](Screenshot_12.png){ width=80% }

## QUIC поверх UDP: Initial (к серверу)
Фильтр: `quic`  
Ключевые поля:
- Long Header, Packet Type: **Initial**
- Version: **1 (0x00000001)**
- DCID Length: **8**
- DCID: **77058adeee4b870c**

![QUIC Initial: параметры заголовка и Connection ID](Screenshot_13.png){ width=80% }

## QUIC: Initial (ответ сервера)
Параметры:
- Packet Type: **Initial**
- SCID Length: **17**
- SCID: **f6b296f219f55030078ddc1e885930e148**

![QUIC Initial: ответный пакет и Source Connection ID](Screenshot_14.png){ width=80% }

# TCP handshake

## Выбор TCP-потока
В Wireshark отфильтрованы TCP пакеты и выбран поток между:
- **192.168.1.69 ↔ 188.184.67.127**

![Общий список TCP-пакетов и выбранный поток](Screenshot_15.png){ width=80% }

## Этап 1: SYN (клиент → сервер)
Клиент инициирует соединение:
- Src port: **50071** → Dst port: **80**
- Flags: **SYN**
- Seq: **0**
- TCP header: **32 байта** (есть опции)

![TCP SYN от клиента](Screenshot_16.png){ width=80% }

## Этап 2–3: SYN/ACK и ACK
**SYN/ACK** (сервер → клиент):
- Flags: **SYN, ACK**
- Ack: **1**, Window: **63744**

**ACK** (клиент → сервер):
- Flags: **ACK**
- Seq: **1**, Ack: **1**

![TCP ACK — завершение handshake](Screenshot_17.png){ width=80% }

## Завершение соединения и график потока
График потока визуализирует:
- SYN → SYN/ACK → ACK,
- передачу данных,
- завершение сеанса.

![График потока TCP в Wireshark](Screenshot_18.png){ width=80% }

# Итоги

## Вывод
В ходе работы:
- определены параметры сетевого подключения (IP, шлюз, DHCP/DNS, MAC);
- изучены ARP и ICMP на канальном/сетевом уровнях;
- проанализированы HTTP/TCP, DNS/UDP, QUIC/UDP;
- разобран механизм **TCP three-way handshake** и завершение соединения.

Результаты подтверждают корректную работу сетевого стека Windows и типовой обмен данными в локальной сети и при доступе к внешним ресурсам.
