# Кинопоиск. Простые шаблоны.

Сегодня мы выведем в базовом варианте все переданные переменные в наших шаблонах.
Пока не красиво, но зато просто и понятно.

1. ## Скачаем базу данных и медиа файлы.
   * На этом этапе важно удостоверится, что все поля моделей у учеников
   совпадают с полями в моделях из курса, а также названия приложений соответствуют моим.
   
   * Удалите файл базы данных `db.sqlite3`
   
   * Скачайте архив `kinopoisk_data.zip` из репозитория со [шпаргалками](https://github.com/xlartas/it-compot-backend-methods) 
   и поместите содержимое в папку по пути `MEDIA_ROOT`.
      > Файл `models.py` просто на всякий случай. Его переносить не надо.
   
   **Таким, образом у вас будет база данных с уже 
   заполненными фильмами, актерами и режиссерами и медиа-файлы для них.**
   * **Суперпользователь** _login:password_ `123:123` 
   
   #### Проверьте админку на наличие объектов и правильных ссылок в полях изображений.
   >Если что-то не так, проверьте ваши модели на соответствие моим. Если нашли ошибку 
   удалите миграции и базу данных, создайте файлы миграций и снова переместите базу данных.
   Проверьте переменную MEDIA_ROOT.

   * ### Если названия приложений и моделей отличаются от моих:
       > Переименовать) или...
   
       Используем папку `dump` внутри архива и корректируем названия таблиц и полей внутри каждого `json`.<br>
       Далее загружаем эти дампы используя:<br>
       `python manage.py loaddata dump/kinopoisk_movieperson.json`<br>
       И так для каждого файла. Если ошибки значит дамп не соответствует вашим таблицам в бд и/или моделям.
   
2. ## Допишем наши шаблоны.
   Каждый шаблон будет наследоваться от базового шаблона.
   Так же, для лучшего понимания и запоминания, будем выводить в самом простом виде
   переданные переменные.<br>
   Суть та же стараемся меньше подсказывать.<br>
   Неважно какие тэги использовать главное вывести правильно.
    ```html
    <!-- kinopoisk/main.html -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | Главная{% endblock %}
    {% block content %}
        {# Тут переменных пока нет #}
    {% endblock %}
    ```
    ```html
    <!-- kinopoisk/movie_list.html -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | Фильмы{% endblock %}
    {% block content %}
        <h2>Фильмы</h2>
        {% for movie in movies %}
            <p>{{ movie.title }}</p>
        {% endfor %}
    {% endblock %}
    ```
    ```html
    <!-- kinopoisk/person_list.html -->
    <!-- Помним, что шаблон для всех режиссеров и для всех актеров одновременно -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | {{ title }}{% endblock %}
    {% block content %}
        <h2>{{ title }}</h2>
        {% for person in persons %}
            <p>{{ person.name }}</p>
        {% endfor %}
    {% endblock %}
    ```
    ```html
    <!-- kinopoisk/genre_list.html -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | Жанры{% endblock %}
    {% block content %}
        <h2>Жанры</h2>
        {% for genre in genres %}
            <p>{{ genre.name }}</p>
        {% endfor %}
    {% endblock %}
    ```
    ```html
    <!-- kinopoisk/movie_detail.html -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | {{ movie.title }}{% endblock %}
    {% block content %}
        <h2>{{ movie.title }}</h2>
        <p>{{ movie.description }}</p>
    {% endblock %}
    ```
    ```html
    <!-- kinopoisk/person_detail.html -->
    <!-- Помним, что этот шаблон для режиссера и для актера одновременно -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | {{ person.name }}{% endblock %}
    {% block content %}
        <h2>{{ person.name }}</h2>
        <h3>Участвовал в:</h3>
        {% for movie in movies %}
            <p>{{ movie.title }}</p>
        {% endfor %}
    {% endblock %}
    ```
    ```html
    <!-- kinopoisk/genre_detail.html -->
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | {{ genre.name }}{% endblock %}
    {% block content %}
        <h2>{{ genre.name }}</h2>
        {% for movie in movies %}
            <p>{{ movie.title }}</p>
        {% endfor %}
    {% endblock %}
    ```
    ### Проверьте, что все корректно работает и объекты везде выводятся. 

## Загрузите проект на гит если еще не загружали.
## Переходите к следующему занятию

## Подведите итоги.
># git push...
