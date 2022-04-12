_Здесь будет TOC директорий вики_

## Обзор проекта

### Введение

В качестве темы проектной работы была выбрана автоматизация процесса обновления ленты мероприятий [Сайта УАПиПО ГУУ](http://pmo.guu.ru/all-events/).

На ней выкладываются актуальные конкурсы, акселерационные и инкубационные программы, хакатоны, кейс-чемпионаты, конференции и другие события, с помощью которых обучающиеся могут значительно ускорить разработку и повысить качество собственных проектов. Сейчас обновление ленты мероприятий происходит исключительно в ручном режиме, что занимает много времени у администратора сайта.

**Гипотеза**, на которой строится проект, состоит в том, что существующий процесс обновления ленты мероприятий сайта УАПиПО может быть автоматизирован, а сама автоматизация, с одной стороны, позволит значительно **сократить трудозатраты** вовлеченных сотрудников, а с другой стороны, поможет обеспечить **своевременное появление мероприятий на сайте**, что даст обучающимся больше времени на подготовку.

Работа над проектом ведется в 3-х основных направлениях, каждое из которых представлено отдельным `github`-репозиторием:

1. **Парсеры** сайтов-источников ([Scraper](https://github.com/Project-Frogger/Scraper))
2. **База данных**, в которой обрабатывается и хранится собранная информация ([Database](https://github.com/Project-Frogger/Database)) - перенос в процессе
3. **Плагин** для Wordpress, который будет являться основным интерфейсом пользователя для работы с мероприятиями ([Plugin](https://github.com/Project-Frogger/Plugin))

<br>

### Как происходит сейчас

Ниже представлена AS IS модель процесса, который мы планируем автоматизировать:

![image](https://user-images.githubusercontent.com/85339045/163034294-e472c6ba-5319-40f7-8021-26a4ccde1979.png)

![image](https://user-images.githubusercontent.com/85339045/163034401-b57b3d7d-2d85-4d1a-992c-8ce95e16d5f8.png)

![image](https://user-images.githubusercontent.com/85339045/163034641-4940ec05-30ea-427a-8f8f-51b63a00c233.png)

![image](https://user-images.githubusercontent.com/85339045/163034662-72569a9c-51f2-4fbf-a58c-4f36e3785894.png)

![image](https://user-images.githubusercontent.com/85339045/163034679-24cb9687-5535-4189-a046-870bf3fc2f0f.png)

<br>

### Как будет (и что уже есть)

#### Публикация автоматически собранных материалов

_1. Парсеры собирают информацию из сайтов-источников и складывают ее в базу данных:_

  - https://rb.ru/chance/
  - https://generation-startup.ru/startups/#accelerators
  - https://generation-startup.ru/calendar/
  - https://changellenge.com/championships/

_2. Информация обрабатывается внутри базы:_

  - Происходит проверка на наличие дублей
  - Создается транслит названия для ярлыка мероприятия
  - Вычисляются даты проведения по исходной строке
  - Получившиеся данные заносятся в целевую таблицу

_3. Определенная пользователем выборка мероприятий попадает в плагин:_

Внутри плагина уже определены несколько **категорий**:
  - Все (это все мероприятия кроме _архивированных_ и находящихся в _корзине_)
  - Опубликованные
  - Черновики
  - Архив
  - Корзина

Помимо этого пользователь может делать выборку с помощью **фильтрации**:

- Посредством фильтров, находящихся в заголовках столбцов

![image](https://user-images.githubusercontent.com/85339045/163049549-1ebcb45e-be71-46bd-93c5-65ac68cced20.png)

- Посредством прямых SQL-запросов к базе

![image](https://user-images.githubusercontent.com/85339045/163050161-cece5ff6-b0b5-408f-85bb-8aad2ed93297.png)

При использовании любого из методов формируется часть SQL-запроса (только часть, относящаяся к команде WHERE), которую можно сохранить в системе и потом при необходимости к ней обращаться:

![image](https://user-images.githubusercontent.com/85339045/163050235-0fd38648-c039-4c2a-9d81-c4b3e3d1b369.png)

_4. Пользователь редактирует мероприятия с помощью их карточки:_

![image](https://user-images.githubusercontent.com/85339045/163050762-b7fdca37-bb42-4ceb-b856-c2f77406e826.png)

_5. Запись публикуется, если в карточке выбран пункт `Опубликовано?`_

<br>

#### Публикация собранных вручную материалов

_1. Пользователь прописывает информацию о мероприятии в новой карточке с помощью пункта меню `Добавить запись`:_

![image](https://user-images.githubusercontent.com/85339045/163051301-f1710c4b-8c73-4c6f-adfc-1d002604d499.png)

_2. При нажатии кнопки `Сохранить` происходит:_

- Проверка наличия этого мероприятия в базе
- Если мероприятие уже есть, то предлагает перейти к редктированию его карточки
- Если его в базе еще нет, то происходит сохранение новой записи

_3. Запись публикуется, если в карточке выбран пункт `Опубликовано?`_


**С полным интерактивным макетом плагина можно ознакомиться [здесь](https://www.figma.com/proto/TXm86pkiun9Sf7NkPUoxER/Untitled?page-id=402%3A5&node-id=405%3A67&viewport=241%2C48%2C0.11&scaling=contain&starting-point-node-id=405%3A67).
**
