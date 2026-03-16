---
## Front matter
title: "Отчёт по лабораторной работе 16"
subtitle: " Базовая защита от атак типа «brute force»"
author: "Абрикосов Артем"

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

Получить навыки работы с программным средством Fail2ban для обеспечения базовой защиты от атак типа «brute force».

## Защита сервера с помощью Fail2ban

1. На сервере выполнена установка пакета Fail2ban с использованием менеджера пакетов DNF.

   dnf -y install fail2ban

2. После завершения установки сервис Fail2ban был запущен и добавлен в автозагрузку.

   systemctl start fail2ban  
   systemctl enable fail2ban

3. В дополнительном терминале был запущен просмотр журнала событий Fail2ban для контроля работы сервиса в реальном времени.

   tail -f /var/log/fail2ban.log

   В журнале зафиксирован запуск сервиса, инициализация компонентов и создание базы данных.

   ![Просмотр журнала Fail2ban после запуска сервиса](Screenshot_1.png){ #fig:009 width=80% }

4. Для задания локальной конфигурации Fail2ban создан файл:

   /etc/fail2ban/jail.d/customisation.local

5. В созданном файле локальной конфигурации выполнена настройка параметров защиты SSH:
   - задано время блокировки IP-адреса 3600 секунд (1 час);
   - включена защита стандартного SSH-сервиса;
   - включена защита от SSH DDoS-атак;
   - активирован модуль selinux-ssh.

   ![Настройка защиты SSH в customisation.local](Screenshot_2.png){ #fig:010 width=80% }

6. После внесения изменений сервис Fail2ban был перезапущен.

   systemctl restart fail2ban

7. Повторно выполнен просмотр журнала событий Fail2ban. В журнале подтверждено создание и запуск jail для sshd, sshd-ddos и selinux-ssh.

   ![Журнал Fail2ban после включения защиты SSH](Screenshot_3.png){ #fig:011 width=80% }

8. В файле /etc/fail2ban/jail.d/customisation.local выполнено включение модулей защиты веб-сервера Apache, включая фильтры для аутентификации, ботов, скриптовых атак, переполнений и уязвимости Shellshock.

   ![Включение защиты HTTP-сервисов Apache](Screenshot_4.png){ #fig:012 width=80% }

9. После изменения конфигурации сервис Fail2ban был перезапущен.

   systemctl restart fail2ban

10. В журнале событий Fail2ban подтверждён успешный запуск jail для всех включённых HTTP-модулей Apache.

    ![Журнал Fail2ban после включения защиты HTTP](Screenshot_5.png){ #fig:013 width=80% }

11. Далее в файле /etc/fail2ban/jail.d/customisation.local была включена защита почтовых сервисов:
    - Postfix;
    - Postfix RBL;
    - Dovecot;
    - Postfix SASL.

    ![Включение защиты почтовых сервисов](Screenshot_6.png){ #fig:014 width=80% }

12. После включения защиты почтовых сервисов выполнен перезапуск Fail2ban.

    systemctl restart fail2ban

13. В журнале событий Fail2ban зафиксирован успешный запуск jail для всех почтовых сервисов, а также подтверждено корректное функционирование системы защиты.

    ![Журнал Fail2ban после включения защиты почты](Screenshot_7.png){ #fig:015 width=80% }

## Проверка работы Fail2ban

1. На сервере выполнена проверка общего статуса Fail2ban с выводом количества активных jail и их перечня.

   fail2ban-client status

2. Выполнена проверка состояния защиты SSH (jail `sshd`) с отображением статистики ошибок и заблокированных адресов.

   fail2ban-client status sshd

3. Для SSH установлено максимальное количество ошибок аутентификации, равное 2.

   fail2ban-client set sshd maxretry 2

   ![Проверка статуса Fail2ban и настройка maxretry для sshd](Screenshot_8.png){ #fig:016 width=80% }

4. С клиентской машины выполнены попытки подключения по SSH к серверу с вводом неправильного пароля для генерации событий блокировки.

5. На сервере повторно проверен статус jail `sshd`. Убедились, что адрес клиента попал в список заблокированных.

6. Выполнена разблокировка IP-адреса клиента с использованием команды снятия бана.

   fail2ban-client set sshd unbanip 192.168.1.30

7. После разблокировки повторно проверен статус защиты SSH. Убедились, что список заблокированных адресов очищен.

   fail2ban-client status sshd

   ![Блокировка IP-адреса клиента и снятие блокировки через unbanip](Screenshot_9.png){ #fig:017 width=80% }

8. На сервере внесено изменение в файл локальной конфигурации `/etc/fail2ban/jail.d/customisation.local`: в секции `[DEFAULT]` добавлено игнорирование адреса клиента.

   [DEFAULT]  
   bantime = 3600  
   ignoreip = 127.0.0.1/8 192.168.1.30

   ![Добавление ignoreip в customisation.local](Screenshot_10.png){ #fig:018 width=80% }

9. После внесения изменений выполнен перезапуск сервиса Fail2ban.

   systemctl restart fail2ban

10. Выполнен просмотр журнала событий Fail2ban. В журнале подтверждено, что указанный IP-адрес клиента игнорируется механизмом блокировки.

    tail -f /var/log/fail2ban.log

    ![Журнал Fail2ban с подтверждением игнорирования IP-адреса клиента](Screenshot_11.png){ #fig:019 width=80% }

11. С клиентской машины повторно выполнены попытки подключения по SSH с неправильным паролем, после чего на сервере проверен статус jail `sshd`. Убедились, что блокировка для добавленного в `ignoreip` адреса не применяется.


## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. На виртуальной машине `server` выполнен переход в каталог `/vagrant/provision/server/`, после чего создана структура каталогов для хранения конфигурации Fail2ban в составе provisioning.

   cd /vagrant/provision/server  
   mkdir -p /vagrant/provision/server/protect/etc/fail2ban/jail.d

2. Файл локальной конфигурации Fail2ban скопирован в подготовленный каталог для последующего применения при развертывании.

   cp -R /etc/fail2ban/jail.d/customisation.local /vagrant/provision/server/protect/etc/fail2ban/jail.d/

3. В каталоге `/vagrant/provision/server` создан исполняемый provisioning-скрипт `protect.sh`, в который внесены команды установки Fail2ban, копирования конфигурационных файлов, восстановления контекстов SELinux и запуска сервиса.

   ![Скрипт protect.sh для provisioning защиты Fail2ban](Screenshot_12.png){ #fig:020 width=80% }

4. Для применения provisioning-скрипта при загрузке виртуальной машины `server` в конфигурационном файле `Vagrantfile` добавлена секция, запускающая `provision/server/protect.sh` в составе процесса развертывания.


# Вывод

В ходе лабораторной работы была настроена и проверена система защиты сервера с использованием Fail2ban.  
Произведена установка и запуск сервиса, выполнена настройка защиты для SSH, веб-служб и почтовых сервисов.  
Проверена работа механизмов блокировки и разблокировки IP-адресов, а также настройка исключений с использованием параметра ignoreip.  
Дополнительно реализовано автоматическое применение конфигурации Fail2ban при развертывании виртуальной машины с помощью provisioning-скрипта Vagrant.


# Контрольные вопросы

1. Поясните принцип работы Fail2ban.  
   Fail2ban анализирует журналы системных и сетевых сервисов (например, SSH, Apache, Postfix) на наличие подозрительной активности, такой как многократные ошибки аутентификации.  
   При превышении заданного порога ошибок Fail2ban автоматически применяет меры защиты — как правило, временно блокирует IP-адрес атакующего с использованием правил брандмауэра.

2. Настройки какого файла более приоритетны: jail.conf или jail.local?  
   Более приоритетным является файл jail.local.  
   Файл jail.conf содержит настройки по умолчанию и не рекомендуется для прямого редактирования, тогда как jail.local и файлы в каталоге jail.d используются для переопределения и дополнения базовой конфигурации.

3. Как настроить оповещение администратора при срабатывании Fail2ban?  
   Оповещение администратора настраивается с помощью параметров destemail, sender и action в конфигурационных файлах Fail2ban.  
   В качестве действия может использоваться, например, action = %(action_mwl)s, которое отправляет письмо с информацией о блокировке и фрагментами логов.

4. Поясните построчно настройки по умолчанию в конфигурационном файле /etc/fail2ban/jail.conf, относящиеся к веб-службе.  
   - enabled — включает или отключает конкретный jail для веб-службы.  
   - port — указывает порт или сервис, для которого применяется защита (обычно http или https).  
   - filter — задаёт фильтр, определяющий шаблоны атак в логах веб-сервера.  
   - logpath — путь к журналу веб-сервера, который анализируется Fail2ban.  
   - maxretry — количество допустимых ошибок перед блокировкой.  
   - findtime — интервал времени, в течение которого учитываются ошибки.  
   - bantime — время блокировки IP-адреса.

5. Поясните построчно настройки по умолчанию в конфигурационном файле /etc/fail2ban/jail.conf, относящиеся к почтовой службе.  
   - enabled — включает защиту почтового сервиса.  
   - port — указывает используемые почтовые порты (smtp, pop3, imap и др.).  
   - filter — определяет фильтр для анализа почтовых логов.  
   - logpath — путь к журналам почтового сервера (Postfix, Dovecot).  
   - maxretry — максимальное число неудачных попыток аутентификации.  
   - bantime — длительность блокировки IP-адреса.  

6. Какие действия может выполнять Fail2ban при обнаружении атакующего IP-адреса? Где можно посмотреть описание действий для последующего использования в настройках Fail2ban?  
   Fail2ban может блокировать IP-адрес с помощью iptables, firewalld, nftables, а также отправлять уведомления администратору или выполнять пользовательские скрипты.  
   Описание доступных действий можно найти в каталоге /etc/fail2ban/action.d.

7. Как получить список действующих правил Fail2ban?  
   Список активных jail и применяемых правил можно получить командой просмотра общего статуса Fail2ban, которая отображает все включённые модули защиты.

8. Как получить статистику заблокированных Fail2ban адресов?  
   Статистика заблокированных адресов доступна при просмотре статуса конкретного jail, где отображается общее количество блокировок и список заблокированных IP-адресов.

9. Как разблокировать IP-адрес?  
   Разблокировка IP-адреса выполняется с помощью команды Fail2ban для соответствующего jail, указывая IP-адрес, который необходимо исключить из списка заблокированных.
