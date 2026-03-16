---
## Front matter
title: "Отчёт по лабораторной работе 5"
subtitle: "Расширенная настройка HTTP-сервера Apache"
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

Приобретение практических навыков по расширенному конфигурированию HTTPсервера Apache в части безопасности и возможности использования PHP.

# Выполнение

## Конфигурирование HTTP-сервера для работы через протокол HTTPS

1. На виртуальной машине server выполнен переход в рабочий каталог проекта и запуск ВМ. После входа под пользователем произведён переход в режим суперпользователя.

2. В каталоге /etc/ssl создан каталог private и выполнена привязка к каталогу /etc/pki/tls/private. Далее выполнена генерация ключа и сертификата для сайта www.akabrikosov.net.

   ![Генерация сертификата](Screenshot_1.png){ #fig:001 width=80% }

   В процессе генерации были указаны:
   – Country Code: RU  
   – State: Russia  
   – Locality: Moscow  
   – Organization: akabrikosov  
   – Organizational Unit: akabrikosov  
   – Common Name: akabrikosov.net  
   – Email: akabrikosov@akabrikosov.net

   После генерации файлы ключа и сертификата появились в каталоге /etc/ssl/private.

3. Сертификат перемещён в каталог /etc/pki/tls/certs.

4. Внесены изменения в конфигурационный файл виртуального хоста Apache  
   `/etc/httpd/conf.d/www.akabrikosov.net.conf`.

   ![Конфигурационный файл виртуального хоста](Screenshot_2.png){ #fig:003 width=80% }

### Пояснение содержимого конфигурационного файла

**Блок HTTP (порт 80)**

- `<VirtualHost *:80>` — определение виртуального хоста для обслуживания HTTP-трафика.  
- `ServerAdmin webmaster@akabrikosov.net` — адрес администратора сайта.  
- `DocumentRoot /var/www/html/www.akabrikosov.net` — корневой каталог веб-сайта.  
- `ServerName` и `ServerAlias` — доменные имена веб-ресурса.  
- `ErrorLog` и `CustomLog` — пути к логам ошибок и обращений.  
- `RewriteEngine on` — включение механизма перенаправления.  
- `RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R=301,L]` — автоматическое перенаправление всего трафика HTTP на HTTPS.

**Блок HTTPS (порт 443)**

- `<IfModule mod_ssl.c>` — проверка наличия SSL-модуля.  
- `<VirtualHost *:443>` — определение виртуального хоста HTTPS.  
- `SSLEngine on` — включение поддержки SSL.  
- `DocumentRoot` — корневая директория сайта.  
- `ServerName`, `ServerAlias` — доменные имена.  
- `ErrorLog`, `CustomLog` — файлы логов.  
- `SSLCertificateFile` — путь к SSL-сертификату.  
- `SSLCertificateKeyFile` — путь к закрытому ключу SSL.

5. Настроен межсетевой экран для разрешения HTTPS-трафика.

6. Выполнен перезапуск службы httpd.

7. На виртуальной машине client введён адрес https://www.akabrikosov.net, выполнено автоматическое перенаправление с HTTP на HTTPS.

   ![Переход на защищённый протокол](Screenshot_3.png){ #fig:004 width=80% }

   Просмотр содержимого сертификата через браузер:

   ![Просмотр сертификата в браузере](Screenshot_4.png){ #fig:005 width=80% }

## Конфигурирование HTTP-сервера для работы с PHP

1. На сервер установлены пакеты для работы с PHP.

2. В каталоге /var/www/html/www.akabrikosov.net создан файл index.php с содержимым:

   ![index.php](Screenshot_5.png){ #fig:006 width=80% }

3. Права доступа в каталог веб-контента изменены на apache:apache.

4. Восстановлены контексты безопасности SELinux для каталогов /etc и /var/www.

5. Перезапущен HTTP-сервер httpd.

6. На клиенте подтверждена работа PHP посредством phpinfo():

   ![phpinfo()](Screenshot_6.png){ #fig:007 width=80% }

## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. В каталоге `/vagrant/provision/server/http` обновлены конфигурационные файлы веб-сервера и веб-контента. Скопированы:

   – конфигурации Apache;  
   – содержимое каталога /var/www/html;  
   – SSL-ключ www.akabrikosov.net.key;  
   – SSL-сертификат www.akabrikosov.net.crt.

2. В скрипт `/vagrant/provision/server/http.sh` добавлены команды установки PHP и разрешения HTTPS в межсетевом экране.

   ![Фрагмент скрипта с изменениями](Screenshot_7.png){ #fig:008 width=80% }

# Вывод

В ходе работы был развернут лабораторный стенд на базе Vagrant и VirtualBox в среде Windows.
Настроены сценарии автоматического развертывания с использованием скриптов 01-user.sh и 01-hostname.sh.
Проверены корректность создания пользователя, установка hostname и работа подключения по SSH.

# Контрольные вопросы

1. В чём отличие HTTP от HTTPS
   HTTP передаёт данные в открытом виде, без шифрования.
   HTTPS использует SSL/TLS, обеспечивая шифрование и защиту передаваемой информации.

2. Каким образом обеспечивается безопасность контента веб-сервера при работе через HTTPS
   Безопасность обеспечивается с помощью SSL/TLS-сертификата, который шифрует трафик, исключая возможность его перехвата или подмены.
   Также выполняется проверка подлинности сервера на основе сертификата.

3. Что такое сертификационный центр? Приведите пример
   Сертификационный центр — организация, выпускающая и подписывающая цифровые сертификаты, подтверждающие подлинность веб-ресурсов.
   Примеры: Let's Encrypt, GlobalSign, DigiCert.
