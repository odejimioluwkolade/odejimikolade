---
## Front matter
title: "Отчёт по лабораторной работе 4"
subtitle: "Базовая настройка HTTP-сервера Apache"
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

Приобретение практических навыков по установке и базовому конфигурированию HTTP-сервера Apache.

# Выполнение

## Установка и базовая настройка HTTP-сервера

1. Операционная система загружена, выполнен переход в рабочий каталог проекта:
   `cd /var/tmp/user_name/vagrant`

2. Запущена виртуальная машина **server**:
   `vagrant up server`

3. На виртуальной машине выполнен вход под пользователем, открыт терминал и получены права суперпользователя.

4. Установлена группа пакетов стандартного веб-сервера:
   `dnf -y groupinstall "Basic Web Server"`

## Базовое конфигурирование HTTP-сервера

1. Изучены конфигурационные файлы в каталогах `/etc/httpd/conf` и `/etc/httpd/conf.d`.  
   Отмечены ключевые параметры: модули, виртуальные хосты, настройки директорий, механизмы обработки запросов.

2. В межсетевой экран добавлено правило, разрешающее HTTP:
   `firewall-cmd --add-service=http --permanent`

   ![Разрешение сервиса http в firewalld](Screenshot_1.png){ #fig:004 width=80% }

3. В дополнительном терминале запущен расширенный лог системных сообщений:
   `journalctl -x -f`

4. Активирован и запущен HTTP-сервер:
   `systemctl enable httpd`
   `systemctl start httpd`

   По журналу подтвержден успешный запуск сервиса.

## Анализ работы HTTP-сервера

1. Запущена виртуальная машина **client**:
   `vagrant up client`

2. На сервере просматривается лог ошибок:
   `tail -f /var/log/httpd/error_log`

3. На сервере включён мониторинг доступа:
   `tail -f /var/log/httpd/access_log`

4. На клиенте в браузере открыт адрес `http://192.168.1.1`.  
   Отобразилась тестовая страница Rocky Linux.

   ![HTTP Server Test Page](Screenshot_2.png){ #fig:007 width=80% }

5. В access_log отразились запросы клиента:

   ![Записи access_log после обращения клиента](Screenshot_3.png){ #fig:008 width=80% }

   Логи подтверждают корректную обработку запросов и успешную отдачу статического контента сервером.

## Настройка виртуального хостинга для HTTP-сервера

1. Остановлена работа DNS-сервера для внесения изменений:  
   `systemctl stop named`

2. В файл прямой DNS-зоны `/var/named/master/fz/akabrikosov.net` добавлена запись:

```
www     A     192.168.1.1
```

![Прямая зона](Screenshot_4.png){ #fig:009 width=80% }

3. В файл обратной зоны `/var/named/master/rz/192.168.1` добавлена запись:

```
1     PTR     [www.akabrikosov.net](http://www.akabrikosov.net).
```

![Обратная зона](Screenshot_5.png){ #fig:010 width=80% }

4. Удалены журнальные файлы зон:
`akabrikosov.net.jnl`, `192.168.1.jnl`.

5. DNS-сервер перезапущен:  
`systemctl start named`

## Создание файлов виртуальных хостов Apache

1. Созданы файлы конфигурации:

```
/etc/httpd/conf.d/server.akabrikosov.net.conf
/etc/httpd/conf.d/[www.akabrikosov.net.conf](http://www.akabrikosov.net.conf)
```

2. В `server.akabrikosov.net.conf` внесено:

```
<VirtualHost *:80>
ServerAdmin [webmaster@akabrikosov.net](mailto:webmaster@akabrikosov.net)
DocumentRoot /var/www/html/server.akabrikosov.net
ServerName server.akabrikosov.net
ErrorLog logs/server.akabrikosov.net-error_log
CustomLog logs/server.akabrikosov.net-access_log common </VirtualHost>
```

![Конфигурация server.akabrikosov.net](Screenshot_6.png){ #fig:011 width=80% }

3. В `www.akabrikosov.net.conf` внесено:

```
<VirtualHost *:80>
ServerAdmin [webmaster@akabrikosov.net](mailto:webmaster@akabrikosov.net)
DocumentRoot /var/www/html/[www.akabrikosov.net](http://www.akabrikosov.net)
ServerName [www.akabrikosov.net](http://www.akabrikosov.net)
ErrorLog logs/www.akabrikosov.net-error_log
CustomLog logs/www.akabrikosov.net-access_log common </VirtualHost>
```

![Конфигурация www.akabrikosov.net](Screenshot_7.png){ #fig:012 width=80% }

## Создание каталогов и контента веб-сайтов

1. Для `server.akabrikosov.net` создан каталог и файл:

```
/var/www/html/server.akabrikosov.net/index.html
```

Содержимое страницы:

```
Welcome to the server.akabrikosov.net server
```

![Создание каталога и index.html](Screenshot_8.png){ #fig:013 width=80% }

2. Для `www.akabrikosov.net` создан каталог и файл:

```
/var/www/html/[www.akabrikosov.net/index.html](http://www.akabrikosov.net/index.html)
```

Содержимое страницы:

```
Welcome to the [www.akabrikosov.net](http://www.akabrikosov.net) server
```

## Настройка SELinux и прав доступа

1. Назначены правильные владельцы:

`chown -R apache:apache /var/www`

2. Восстановлены контексты SELinux:

```
restorecon -vR /etc
restorecon -vR /var/named
restorecon -vR /var/www
```

## Проверка работы виртуальных хостов

После перезапуска Apache:

`systemctl restart httpd`

Проверка из браузера клиента:

### server.akabrikosov.net

![Доступ к server.akabrikosov.net](Screenshot_9.png){ #fig:015 width=80% }

### www.akabrikosov.net

![Доступ к www.akabrikosov.net](Screenshot_10.png){ #fig:016 width=80% }

Оба виртуальных хоста отвечают корректно.

## Добавление конфигураций в окружение Vagrant

1. Скопированы файлы конфигураций

2. Создан файл `http.sh`:

Содержимое скрипта:

![Содержимое http.sh](Screenshot_11.png){ #fig:017 width=80% }

# Вывод

Был настроен виртуальный хостинг для двух доменных имён, включающих серверные и клиентские зоны DNS. Созданы конфигурации Apache для отдельных виртуальных хостов, подготовлены каталоги с веб-контентом и настроены корректные права доступа и контексты SELinux.
Оба веб-сервера успешно открываются по адресам **server.akabrikosov.net** и **www.akabrikosov.net**, что подтверждает правильную работу DNS и HTTP-сервера.

# Контрольные вопросы

1. Через какой порт по умолчанию работает Apache  
   Apache по умолчанию использует порт **80** для протокола HTTP.  
   При включённом HTTPS дополнительно применяется порт **443**.

2. Под каким пользователем запускается Apache и к какой группе относится этот пользователь  
   В дистрибутивах семейства RHEL/Rocky Linux веб-сервер запускается от имени пользователя **apache**,  
   который относится к одноимённой группе **apache**.  
   Это ограничивает права сервиса и повышает безопасность.

3. Где располагаются лог-файлы веб-сервера? Что можно по ним отслеживать  
   Основные журналы находятся в каталоге:  
   **/var/log/httpd/**  
   Здесь размещаются файлы `error_log` и `access_log`.  
   По ним можно отслеживать ошибки веб-сервера, выполнение запросов клиентов, IP-адреса, коды ответов, время доступа и прочую диагностическую информацию.

4. Где по умолчанию содержится контент веб-серверов  
   Файлы веб-страниц хранятся в каталоге:  
   **/var/www/html**  
   Для виртуальных хостов создаются соответствующие подкаталоги.

5. Каким образом реализуется виртуальный хостинг? Что он даёт  
   Виртуальный хостинг реализуется через конфигурацию отдельных файлов внутри  
   **/etc/httpd/conf.d**, где каждый `<VirtualHost>` определяет свой сайт с уникальными параметрами: доменным именем, логами и каталогом DocumentRoot.  
   Он позволяет размещать несколько сайтов на одном сервере и IP-адресе, изолируя их настройки и контент.
