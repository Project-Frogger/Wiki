# Перенос сайта

[![back](https://github.com/Project-Frogger/Wiki/blob/main/img/common/back.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/start-config.md) [![to-main](https://github.com/Project-Frogger/Wiki/blob/main/img/common/to-main.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/main-server.md)

**Содержание:**

- [Перенос сайта](#перенос-сайта)
  - [1. Получение содержимого сайта](#1-получение-содержимого-сайта)
  - [2. Перенос содержимого на сервер (с установкой Wordpress)](#2-перенос-содержимого-на-сервер-с-установкой-wordpress)

## 1. Получение содержимого сайта

Для создания резервной копии сайта, которая потом будет перенесена на новый сервер, будет использоваться плагин [Duplicator](https://snapcreek.com/duplicator).

1. Установить плагин на сайте, содержимое которого будет мигрировано;
2. Перейти по пути `Duplicator` ❯ `Пакеты` ❯ `Создать новый`;
3. Ввести имя пакета;
4. Отметить хранилище по умолчанию (если оно не отмечено) и нажать `Далее`;

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/6.png?raw=true)

1. Дать сканеру выполнить свою работу и проверить результаты;

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/7.png?raw=true)

1. Если состояние пакета устраивает, то нажать на кнопку `Создание`;
2. Дождаться завершения создания пакета;
3. Скачать созданные файлы: архив и `installer.php`.

**Важно**: название архива и файла нельзя менять.

## 2. Перенос содержимого на сервер (с установкой Wordpress)

Скачанные файлы необходимо перенести в директорию, откуда они будут доступны по адресу `https://your-domain/installer.php` или `https://your-domain/your-wp-directory/installer.php`. Например, это может быть `/var/www/html/`.

Далее, надо открыть в браузере ссылку `http://your-domain/installer.php`.

Если все было выполнено верно, то появится следующее изображние:

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/1.png?raw=true)

Как видно, плагин указывает на ошибку - в директорию, в которой находится архив и `installer.php`, нельзя записывать новые файлы. Директорию можно сделать редактируемой с помощью команды:

```console
root@pfrogger:~# sudo chmod a+w <directory>
```

Так полномочия на запись данных будут предоставлены для всех пользователей, групп и других агентов.

Далее, для работы плагины нужно установить ZipArchive или Shell Exec - они позволять ему работать с архивом содержимого сайта. Для установки нужно выполнить команду:

```console
root@pfrogger:~# sudo apt-get install php7.4-zip
```

После чего перезупстить веб-сервер:

```console
root@pfrogger:~# sudo service nginx restart
```

Далее все действия необходимо выполнять в соответствии с указаниями плагина.

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/2.png?raw=true)

На втором шаге в поле `Action` надо выбрать `Create New Database`, хостом оставить `localhost`, задать имя базы, а также имя и пароль для пользователя.

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/3.png?raw=true)

После этого нажать `Test Database` и убедиться, что все тесты пройдены успешно:

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/4.png?raw=true)

На шаге 3 достаточно ввести заголовок сайта (раздел `Title`) и нажать на кнопку `Next`.

На последнем шаге надо убедиться, что на опции `Auto delete installer files after login (recommended)` стоит галочка. Эта опция подразумевает, что сразу после входа на сайта все файлы, использованные при установке (архив и `installer.php`), будут удалены с сервера. Если оставить эти файлы на сервере, то в дальнейшем это может вызвать угрозы безопасности.

![img](https://github.com/Project-Frogger/Wiki/blob/main/img/server/site-migration/5.png?raw=true)

Последние шаги:

- Нажать на **Admin Login**;
- Удалить из `/var/www/html/` любые файлы с расширением `.html`.

[![to-start](https://github.com/Project-Frogger/Wiki/blob/main/img/common/to-start.png?raw=true)](#) [![back](https://github.com/Project-Frogger/Wiki/blob/main/img/common/back.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/start-config.md) [![to-main](https://github.com/Project-Frogger/Wiki/blob/main/img/common/to-main.png?raw=true)](https://github.com/Project-Frogger/Wiki/blob/main/server/main-server.md)
