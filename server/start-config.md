# Начальная настройка

[![next](https://github.com/Project-Frogger/Wiki/blob/main/img/common/next.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/site-migration.md) [![to-main](https://github.com/Project-Frogger/Wiki/blob/main/img/common/to-main.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/main-server.md)

**Содержание:**

- [Начальная настройка](#начальная-настройка)
  - [1. Создание пользователя с административными привилегиями](#1-создание-пользователя-с-административными-привилегиями)
    - [1.1 Создание пользователя](#11-создание-пользователя)
    - [1.2 Предоставление административных прав](#12-предоставление-административных-прав)
    - [1.3 Просмотр пользователей системы](#13-просмотр-пользователей-системы)
  - [2. Установка набора LEMP](#2-установка-набора-lemp)
    - [2.1 Установка Nginx](#21-установка-nginx)
    - [2.2 Установка MySQL](#22-установка-mysql)
    - [2.3 Установка PHP и настройка Nginx для использования процессора PHP](#23-установка-php-и-настройка-nginx-для-использования-процессора-php)
    - [2.4 Создание файла PHP для тестирования конфигурации](#24-создание-файла-php-для-тестирования-конфигурации)

Для подключения к серверу по SSH и работы с ним используется клиент [Putty](https://www.putty.org/).

## 1. Создание пользователя с административными привилегиями

### 1.1 Создание пользователя

Сейчас у пользователя есть единственный супер-пользователь - `root`. Такой пользователь обладает очень широким набором прав, что ставит под угрозу сохранность содержимого сервера - он настолько могущественен, что любое неосторожное движение может привести к проблемам.

Поэтому в первую очередь необходимо создать обычного пользователя системы (т.е. не супер-пользователя) с `sudo`-привилегиями. Такой пользователь сможет выполнять необходимые административные команды, но при этом будет ограничен в своих полномочиях.

Новый пользователь создается с помощью директивы `adduser`:

```console
root@pfrogger:~# adduser <username>
```

Консоль попросит ввести дополнительные данные о создаваемом пользователе, начиная с пароля для пользователя:

```console
New password:
Retype new password:
# Необязательно, нажимте ENTER, чтобы пропустить:
    Full Name []: 
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
# Введите y, если все верно:
Is the information correct? [Y/n]
```

### 1.2 Предоставление административных прав

Теперь пользователю необходимо дать права администратора. Для этого его надо добавить в группу `sudo`. Все пользователи, находящиеся в этой группе, могут использовать команду `sudo`, необходимую для выполнения административных команд (т.е. команд, позволяющих изменять конфигурацию или состояние сервера: редактирование конфигурационных файлов, перезапуск сервера и т.п.).

Для предоставления административных прав используется следующая команда:

```console
root@pfrogger:~# usermod -aG sudo <username>
```

1. `usermod` - команда, редактирующая настройки пользователя.
2. Аргумент `-a` (от слова _append_) значит, что настройки, которые будут заданы, добавятся к уже существующим, а не заменят их.
3. Аргумент `-G` значит, что будут изменены именно настройки групп пользователей.

### 1.3 Просмотр пользователей системы

Для вывода всех пользователей системы можно использовать команду:

```console
less /etc/passwd
```

Она открывает `/etc/passwd`, в котором и содержится информация о локальных пользователях. Этот файл содержит следующую информацию:

![show](https://github.com/Project-Frogger/Wiki/blob/main/img/server/start-config/1.png?raw=true)

1. Имя пользователя
2. Зашифрованный пароль (`x` значит, что пароль хранится в файле `/etc/shadow`)
3. Уникальный идентификатор пользователя (`UID`)
4. Уникальный идентификатар групп пользователя (`GID`)
5. Полное имя
6. Домашняя директория
7. Использумый для входа терминал

<br>

## 2. Установка набора LEMP

Набор `LEMP` — это комплекс ПО, который используется для обслуживания динамических web-страниц и web-приложений. В состав комплекса входит ОС Linux, web-сервер Nginx, СУБД MySQL и язык PHP, выполняющий динамическую обрабокту.

### 2.1 Установка Nginx

Все программное обеспечение, используемое в этой процедуре, берется из заданных по умолчанию хранилищ пакетов Ubuntu. Это означает, что для установки можно использовать набор управления пакетами `apt`.

Поскольку в этом сеансе `apt` будет использоваться впервые, нужно обновить указатель пакетов сервера. Затем нужно выполнить установку Nginx:

```console
root@pfrogger:~# sudo apt update
root@pfrogger:~# sudo apt upgrade
root@pfrogger:~# sudo apt install nginx
```

Обновление пакетов может занять некоторое время. На все задаваемые в процессе вопросы нужно ответить `y`.

Если установка прошла корректно, то при попадании на сервер через браузер (для чего нужно ввести его ip-адрес или доменное имя в адресную строку) будет выведено сообщение:

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/start-config/2.png?raw=true)

### 2.2 Установка MySQL

Для установки СУБД MySQL нужно ввести:

```console
root@pfrogger:~# sudo apt install mysql-server
```

СУБД MySQL установлена, но ее настройка еще не завершена. Для защиты установки в комплект MySQL входит скрипт, запрашивающий подтверждение небезопасных изменений параметров по умолчанию. Надо запустить скрипт, введя следующую команду:

```console
root@pfrogger:~# sudo mysql_secure_installation
```

Скрипт предложит настроить `VALIDATE PASSWORD PLUGIN`.

```console
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
```

**Важно**: эту функцию следует активировать только при наличии разумных оснований. Если она активирована, MySQL будет отклонять пароли, не соответствующие определенным критериям, и выводить сообщение об ошибке. Это может сделать использование СУБД менее удобным, а также вызвать некоторые проблемы при использовании ПО для автоматической настройки учетных данных пользователя MySQL. Отключение этой функции не несет угроз безопасности.

После выбора `No` система задаст еще несколько вопросов, на все из которых надо ответить `y`. В результате будут удалены некоторые анонимные пользователи и тестовая база данных, отключена возможность удаленного входа для пользователя `root` и будут загружены новые правила, чтобы СУБД MySQL немедленно использовала внесенные изменения.

В системах Ubuntu с СУБД MySQL 5.7 (и более поздними версиями) для пользователя `root` MySQL по умолчанию для аутентификации задан `auth_socket`, а не пароль. Во многих случаях это обеспечивает более высокую безопасность и удобство, однако это также может осложнить ситуацию, если нужно предоставить доступ к пользователю внешней программе (например, `phpMyAdmin`).

Заменим `auth_socket` на `mysql_native_password`. Для этого сначала надо открыть командную строку mysql:

```console
root@pfrogger:~# sudo mysql
```

Затем стоит проверить, какой метод аутентификации используют аккаунты пользователей MySQL:

```mysql
SELECT user, authentication_string, plugin, host FROM mysql.user;
```

Будет выведена следующая информация:

```mysql
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| user             | authentication_string                                                  | plugin                | host      |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| debian-sys-maint | $A$005$%.cmwX*_L4.-"j/qSm8TWd/KlO0uN1ODnBFDr960XFXTr58dOW3uvtjI2       | caching_sha2_password | localhost |
| mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| root             |                                                                        | auth_socket           | localhost |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
```

Здесь можно видеть, что `root` действительно использует `auth_socket` для аутентификации. Для настройки аутентификации пользователя используется команда `ALTER USER`:

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'auth-forgger';
```

Для применения всех изменений используется команда `FLUSH PRIVILEGES`, которая просит сервер перезагрузить предоставленные таблицы:

```mysql
FLUSH PRIVILEGES;
```

Проверим метод аутентификации:

```mysql
SELECT user, authentication_string, plugin, host FROM mysql.user;
*********************************************************************************************************************************
Output:

+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| user             | authentication_string                                                  | plugin                | host      |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| debian-sys-maint | $A$005$%.cmwX*_L4.-"j/qSm8TWd/KlO0uN1ODnBFDr960XFXTr58dOW3uvtjI2       | caching_sha2_password | localhost |
| mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| root             | *76D124301881B336511A31492BC9D77F0B962593                              | mysql_native_password | localhost |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
```

В выводе можно увидеть, что теперь пользователь `root` СУБД MySQL проходит аутентификацию с помощью пароля. Убедившись в этом, можно выходить из командной строки mysql:

```mysql
exit
```

В дальнейшем для входа в нее нужно будет использовать команду

```console
root@pfrogger:~# mysql -u root -p
```

Которая попросит ввести пароль для заданного пользователя (в данном случае `root`).

### 2.3 Установка PHP и настройка Nginx для использования процессора PHP

Nginx не поддерживает нативную обработку PHP. Для этой цели потребуется установить `php-fpm` - менеджер процессов для PHP, одну из разновидностей серверного API, работающий не с http-протоколом, а cо специальным fastcgi-протоколом.

Помимо этого, для работы с базами данных обязательно понадобится пакет `php-mysql`.

```console
root@pfrogger:~# sudo apt install php-fpm php-mysql
```

Подтвердить установку PHP, а также уточнить версию можно с помощью команды:

```console
root@pfrogger:~# php -v
*********************************************************************************************************************************
Output:

PHP 7.4.3 (cli) (built: Mar  2 2022 15:36:52) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.3, Copyright (c), by Zend Technologies

```

Для вывода всех установленных PHP-модулей используется:

```console
root@pfrogger:~# php -m
*********************************************************************************************************************************
Output:

[PHP Modules]
calendar
Core
ctype
date
exif
FFI
fileinfo
filter
ftp
gettext
hash
iconv
json
libxml
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
Phar
posix
readline
Reflection
session
shmop
sockets
sodium
SPL
standard
sysvmsg
sysvsem
sysvshm
tokenizer
Zend OPcache
zlib

[Zend Modules]
Zend OPcache

```

После установки всех требуемых компонентов набора LEMP нужно внести еще несколько изменений конфигурации, чтобы Nginx использовал процессор PHP для динамического контента.

Это изменение конфигурации выполняется уровне блока сервера. Нужно создать новый файл конфигурации блока сервера в каталоге `/etc/nginx/sites-available/`. В этом примере новый файл конфигурации блока сервера имеет имя `example.com`:

```console
root@pfrogger:~# sudo nano /etc/nginx/sites-available/example.com
```

Содержимое файла:

```console
server {
        listen 80;
        root /var/www/html;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name example.com;

        location / {try_files $uri $uri/ /index.html /index.php?$args;}

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}
```

- `listen` — определяет, что будет прослушивать порт Nginx. В данном случае он будет прослушивать порт 80, используемый по умолчанию для протокола `HTTP`.
- `root` — определяет корневой каталог документа, где хранятся файлы, обслуживаемые сайтом.
- `index` — задает для Nginx приоритет обслуживания файлов с именем index.php (при наличии) при запросе файла индекса.
- `server_name` — определяет, какой серверный блок должен использоваться для заданного запроса вашего сервера. Эта директива должна указывать на доменное имя или публичный IP-адрес сервера.
- `location /` — первый блок расположения включает директиву `try_files`, которая проверяет наличие файлов, соответствующих запросу `URI`. Если Nginx не сможет найти соответствующий файл, будет возвращена ошибка `404`.
- `location ~ \.php$` — этот блок расположения отвечает за фактическую обработку PHP посредством указания Nginx на файл конфигурации `fastcgi-php.conf` и файл `php7.4-fpm.sock`, который объявляет, какой сокет ассоциирован с `php-fpm`.
- `location ~ /\.ht` — последний блок расположения отвечает за файлы `.htaccess`, которые Nginx не обрабатывает. При добавлении директивы `deny all` из файлов `.htaccess` в корневой каталог документа, они не будут выводиться посетителям.

После добавления содержимого следует сохранить и закрыть файл. Для активации нового серверного блока надо создать символическую ссылку от нового файла конфигурации серверного блока (в каталоге `/etc/nginx/sites-available/`) на каталог `/etc/nginx/sites-enabled/`:

```console
root@pfrogger:~# sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

Затем убрать ссылку на файл конфигурации по умолчанию из каталога `/sites-enabled/`:

```console
root@pfrogger:~# sudo unlink /etc/nginx/sites-enabled/default
```

После протестировать новый файл конфигурации на ошибки синтаксиса:

```console
root@pfrogger:~# sudo nginx -t
*********************************************************************************************************************************
Output:

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Для внесения изменений надо перезагрузить Nginx:

```console
root@pfrogger:~# sudo systemctl reload nginx
```

Это завершает установку и настройку набора LEMP, но все-равно нужно проверить работоспособность установленный элементов.

### 2.4 Создание файла PHP для тестирования конфигурации

Для тестирования нужно создать тестовый файл с именем `info.php` в корневом каталоге документа:

```console
root@pfrogger:~# sudo nano /var/www/html/info.php
```

Содержимое файла:

```console
<?php
phpinfo();
```

В случае, когда все работает корректно, этот код будет возвращать информацию о сервере на странице `your_server_domain_or_IP/info.php`:

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/start-config/3.png?raw=true)

Если на странице есть конфигурация сервера, то обработка PHP с помощью Nginx была реализована успешно.

После подтверждения того, что Nginx корректно отображает страницу, рекомендуется удалить созданный файл, поскольку он может дать неавторизованным пользователям определенную информацию о конфигурации сервера, которая может быть использована при попытке взлома:

```console
root@pfrogger:~# sudo rm /var/www/html/info.php
```

[![to-start](https://github.com/Project-Frogger/Wiki/blob/main/img/common/to-start.png?raw=true)](#) [![next](https://github.com/Project-Frogger/Wiki/blob/main/img/common/next.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/site-migration.md) [![to-main](https://github.com/Project-Frogger/Wiki/blob/main/img/common/to-main.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/main-server.md)
