---
## Front matter
title: "Отчёт по лабораторной работе 3"
subtitle: "Настройка DHCP-сервера"
author: "Одеджими Олуваколаде"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Приобретение практических навыков по установке и конфигурированию DHCP-сервера.

# Выполнение

## Конфигурирование DHCP-сервера

1. Выполнено резервное копирование файла конфигурации DHCP. Создана копия с текущей датой.

2. В файл `/etc/kea/kea-dhcp4.conf` внесены изменения.  
   Изменены параметры domain-name и domain-search:

   {
   "code": 15,
   "data": "akabrisov.net"
   },

   {
   "name": "domain-search",
   "data": "akabrisov.net"
   }

   Также заменён блок описания DNS-серверов:

   {
   "name": "domain-name-servers",
   "data": "192.168.1.1"
   }

   На основе примерного шаблона описана собственная подсеть:

   "subnet4": [
     {
       "id": 1,
       "subnet": "192.168.1.0/24",
       "pools": [ { "pool": "192.168.1.30 - 192.168.1.199" } ],
       "option-data": [
         {
           "name": "routers",
           "data": "192.168.1.1"
         }
       ]
     }
   ]

   Остальные примеры подсетей удалены.

   ![Фрагмент файла kea-dhcp4.conf](Screenshot_1.png){ #fig:006 width=80% }

   ![DNS-опции в конфигурации DHCP](Screenshot_2.png){ #fig:007 width=80% }

3. В конфигурации DHCP-сервера указана привязка к интерфейсу eth1:

   "interfaces-config": {
     "interfaces": [ "eth1" ]
   }

4. Файл конфигурации проверен утилитой проверки синтаксиса. Ошибок не обнаружено.

   ![Проверка конфигурационного файла](Screenshot_3.png){ #fig:008 width=80% }

5. Выполнено обновление конфигурации и включение автоматического запуска службы DHCP.

6. В файл прямой зоны `/var/named/master/fz/akabrisov.net` добавлена запись:

   dhcp A 192.168.1.1

   В файл обратной зоны `/var/named/master/rz/192.168.1` добавлена запись:

   1 PTR dhcp.akabrisov.net.

   В обоих файлах обновлён серийный номер зоны.

   ![Прямая DNS-зона](Screenshot_4.png){ #fig:009 width=80% }

   ![Обратная DNS-зона](Screenshot_5.png){ #fig:010 width=80% }

7. DNS-служба перезапущена.

8. Проверена доступность DHCP-сервера по имени. Ответ получен успешно.

   ![Проверка доступности имени dhcp.akabrisov.net](Screenshot_6.png){ #fig:011 width=80% }

9. В межсетевом экране разрешена работа службы DHCP.

10. Восстановлены контексты SELinux для каталогов `/etc`, `/var/named`, `/var/lib/kea`.

   ![Настройка SELinux и firewall](Screenshot_7.png){ #fig:012 width=80% }

11. На сервере запущен мониторинг системных сообщений.

12. Выполнен запуск службы DHCP.

## Анализ работы DHCP-сервера

1. В каталоге `vagrant/provision/client` создан файл `01-routing.sh`, обеспечивающий маршрутизацию через интерфейс eth1 на клиентской машине. Скрипт подключён в конфигурации Vagrant для client.

2. После запуска виртуальной машины client сервер корректно регистрирует подключение в логе и выдаёт IP-адрес.  
   Сведения отражаются в файле `/var/lib/kea/kea-leases4.csv`.

3. Содержимое файла `kea-leases4.csv`:

   ![Содержимое файла аренды адресов DHCP](Screenshot_9.png){ #fig:013 width=80% }

   **Построчный разбор:**

   *address* — выданный IP-адрес  
   *hwaddr* — MAC-адрес клиента  
   *client_id* — идентификатор клиента DHCP  
   *valid_lifetime* — время аренды в секундах  
   *expire* — момент истечения аренды в формате UNIX-времени  
   *subnet_id* — идентификатор подсети в Kea  
   *fqdn_fwd / fqdn_rev* — флаги обновления DNS  
   *hostname* — имя клиента  
   *state* — состояние аренды  
   *pool_id* — идентификатор пула адресов  

   Повторяющиеся строки показывают продление аренды одного и того же IP-адреса.

4. На клиенте просмотрены параметры сетевых интерфейсов:

   ![Вывод ifconfig на клиенте](Screenshot_8.png){ #fig:014 width=80% }

   ### Интерфейс eth1
   • Получил адрес 192.168.1.30 от DHCP  
   • Маска 255.255.255.0  
   • Broadcast 192.168.1.255  
   • MAC-адрес 08:00:27:e3:b0:b0  
   • Пакеты принимаются и передаются без ошибок  

   ### Интерфейс lo  
   • Стандартный loopback  
   • Адрес 127.0.0.1  

5. Список выданных адресов на сервере совпадает с содержимым `kea-leases4.csv`, что подтверждает корректную работу DHCP-сервера.

## Настройка обновления DNS-зоны

1. На сервере создан каталог для ключей и сгенерирован TSIG-ключ для обновлений DNS.  
   Полученный файл `dhcp_updater.key` помещён в `/etc/named/keys/`.

   ![Создание ключа TSIG](Screenshot_10.png){ #fig:01 width=80% }

2. Файл ключа содержит имя ключа, алгоритм и секрет. Параметр secret далее используется в конфигурации Kea.

3. Настроены права доступа на каталог и файл ключа: владельцем назначен пользователь и группа `named`.

4. В файл `/etc/named.conf` подключён ключ через директиву include.

5. В файле зоны `akabrisov.net` разрешено обновление передней зоны:

   zone "akabrisov.net" IN {  
   type master;  
   file "master/fz/akabrisov.net";  
   update-policy {  
   grant DHCP_UPDATER wildcard *.akabrisov.net A DHCID;  
   };  
   };

   Для обратной зоны аналогично разрешено обновление PTR-записей:

   zone "1.168.192.in-addr.arpa" IN {  
   type master;  
   file "master/rz/192.168.1";  
   update-policy {  
   grant DHCP_UPDATER wildcard *.1.168.192.in-addr.arpa PTR DHCID;  
   };  
   };

   ![Настройка update-policy в зонах](Screenshot_11.png){ #fig:015 width=80% }

6. Проверена корректность конфигурации named.

7. DNS-сервер перезапущен.

8. На стороне Kea создан файл `/etc/kea/tsig-keys.json`.

9. TSIG-ключ переписан в JSON-формате для Kea DHCP-DDNS:

   "tsig-keys": [  
     {  
       "name": "DHCP_UPDATER",  
       "algorithm": "hmac-sha512",  
       "secret": "<секретный ключ>"  
     }  
   ]

   ![Файл tsig-keys.json](Screenshot_12.png){ #fig:016 width=80% }

10. Назначены права доступа и владелец `kea:kea`.

11. Настроен файл `/etc/kea/kea-dhcp-ddns.conf`.  
    В нём добавлены:
    • включение ключей  
    • forward-ddns и reverse-ddns  
    • указание DNS-сервера 192.168.1.1  
    • имя ключа DHCP_UPDATER  

    ![Файл kea-dhcp-ddns.conf](Screenshot_13.png){ #fig:017 width=80% }

12. Владелец файла изменён на kea.

13. Проверена конфигурация DHCP-DDNS.

14. Запущена служба `kea-dhcp-ddns.service` и добавлена в автозагрузку.

15. Проверен статус работы службы — сервис работает корректно.

    ![Запуск kea-dhcp-ddns](Screenshot_14.png){ #fig:018 width=80% }

16. В файл `/etc/kea/kea-dhcp4.conf` добавлены параметры разрешения DDNS-обновлений:

   "dhcp-ddns": {  
     "enable-updates": true  
   },  
   "ddns-qualifying-suffix": "akabrisov.net",  
   "ddns-override-client-update": true,

   ![Фрагмент обновлённого kea-dhcp4.conf](Screenshot_15.png){ #fig:019 width=80% }

17. Проверена корректность конфигурации Kea DHCP.

18. DHCP-сервер перезапущен.

19. Проверен статус — служба работает корректно.

   ![Запуск kea-dhcp](Screenshot_16.png){ #fig:020 width=80% }

20. На клиентской машине интерфейс eth1 был перезапущен для получения новой записи в DNS.

21. В каталоге прямой зоны появился файл `akabrisov.net.jnl`, который содержит бинарные данные обновлений зоны.

## Анализ работы DHCP-сервера после настройки обновления DNS-зоны

На клиенте выполнен запрос:

dig @192.168.1.1 client.akabrisov.net

Результат запроса:

![Результат выполнения dig](Screenshot_17.png){ #fig:021 width=80% }

### Построчный разбор результата

• В заголовке указано, что ответ получен успешно (NOERROR).  
• Сервер, отвечающий на запрос — 192.168.1.1, что соответствует DNS-серверу server.  
• В секции QUESTION перечислено имя: client.akabrisov.net.  
• В секции ANSWER содержится запись вида:

client.akabrisov.net.    IN A    192.168.1.30

Это подтверждает, что DHCP успешно передал данные DDNS-модулю Kea, который внёс A-запись в DNS-зону.

## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. Создан каталог `/vagrant/provision/server/dhcp`, в который помещены конфигурационные файлы из `/etc/kea/`.

2. Аналогично перенесены файлы DNS-сервера из `/var/named` и `/etc/named` в соответствующие каталоги `/vagrant/provision/server/dns`.

3. Создан исполняемый файл `dhcp.sh`, содержащий шаги установки, копирования конфигурации, восстановления контекстов SELinux и запуска сервисов Kea.

   ![Скрипт dhcp.sh для автоматической настройки](Screenshot_18.png){ #fig:022 width=80% }

# Вывод

В ходе выполнения работы настроены DHCP- и DNS-сервисы, обеспечивающие автоматическую выдачу адресов и динамическое обновление записей в прямой и обратной зонах. Реализована передача данных от Kea DHCP к Bind9 через TSIG-ключи, проверена корректность обновлений и возможность разрешения имён клиентов. Конфигурации перенесены в окружение Vagrant для последующей автоматизации развёртывания.

# Контрольные вопросы

1. **В каких файлах хранятся настройки сетевых подключений**
   Настройки сетевых подключений обычно хранятся в каталогах `/etc/sysconfig/network-scripts/` (файлы вида `ifcfg-*`) или в профилях NetworkManager по пути `/etc/NetworkManager/system-connections/`.

2. **За что отвечает протокол DHCP**
   DHCP автоматически назначает IP-адреса и сетевые параметры клиентам в сети, упрощая их конфигурацию и управление.

3. **Поясните принцип работы протокола DHCP. Какими сообщениями обмениваются клиент и сервер**
   DHCP работает по схеме DORA:
   • **Discover** — клиент ищет сервер;
   • **Offer** — сервер предлагает адрес;
   • **Request** — клиент запрашивает предложенный адрес;
   • **Ack** — сервер подтверждает выдачу адреса.

4. **В каких файлах находятся настройки DHCP-сервера и за что они отвечают**
   В Kea настройки находятся в `/etc/kea/kea-dhcp4.conf` (основная конфигурация) и `/etc/kea/kea-dhcp-ddns.conf` (настройки DDNS).
   В Bind9 DHCP-связанные настройки могут быть в файле зон и `named.conf`, если используется DDNS.

5. **Что такое DDNS и для чего применяется**
   DDNS — динамическое обновление DNS-зон. Применяется для автоматического добавления, изменения и удаления DNS-записей при появлении или удалении клиентов, получающих адреса по DHCP.

6. **Какую информацию можно получить с помощью ifconfig. Примеры**
   Утилита показывает параметры сетевых интерфейсов: IP-адреса, маску, MAC-адрес, статистику пакетов.
   Примеры:
   • `ifconfig eth1` — сведения о конкретном интерфейсе;
   • `ifconfig -a` — показать все интерфейсы, включая неактивные.

7. **Какую информацию можно получить с помощью ping. Примеры**
   Утилита проверяет доступность узла, задержку и потери пакетов.
   Примеры:
   • `ping 192.168.1.1` — проверить связность с узлом;
   • `ping -c 5 host` — отправить 5 пакетов;
   • `ping -i 0.2 host` — задать интервал между пакетами.
