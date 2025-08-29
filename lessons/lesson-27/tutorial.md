# Кинопоиск. Шапка и страничка с фильмами

Не используем готовую карточку `bootstrap`, напишите её самостоятельно. 

С Frontend курса, у довольно _**сильных**_ учеников, на мой взгляд,
_**очень слабые**_ знания об элементарном размещении элементов.

Достаточно объяснить 4 свойства:
* `display: flex` - включает использование `flex-direction` `justify-content-center` `gap` `align-items`


1. ## Немного переделаем шапку
    Сделаем ссылки на все фильмы, жанры актеров и режиссеров.
    Мы уже это делали, единственное новое, что я тут использовал это
    `gap-md` и `mt-md`, но для слабых учеников это не сильно нужно на данном этапе.
    Так же я объединил иконки профиля, выхода и смены темы в один блок с `d-flex` чтобы
    на телефоне при разворачивании меню они стояли в ряд. Все делать необязательно.
    ```html
    <!-- Core/includes/header.html -->
    <header>
        ..........
        <div class="collapse navbar-collapse flex-grow-0" 
             id="navbarSupportedContent">
            <ul class="navbar-nav mb-2 mb-lg-0 gap-3 gap-md-1 align-items-center">
                <li class="nav-item mt-3 mt-md-0">
                    <a class="nav-link py-0"
                       href="{% url 'movie_list' %}">
                        Фильмы
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link py-0"
                       href="{% url 'genre_list' %}">
                        Жанры
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link py-0"
                       href="{% url 'actor_list' %}">
                        Актёры
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link py-0"
                       href="{% url 'director_list' %}">
                        Режиссёры
                    </a>
                </li>
                <li class="d-flex justify-content-center gap-2">
                    {% if request.user.is_authenticated %}
                        <div class="nav-item">
                            <a class="py-0"
                               href="{% url 'profile' %}">
                                <img width="25" height="25"
                                     style="filter: invert(0.5)"
                                     src="{% static 'Core/img/user.png' %}" alt="profile">
                            </a>
                        </div>
                        <div class="nav-item my-auto">
                            <a class="py-0"
                               href="{% url 'signout' %}">
                                <img width="25" height="25"
                                     style="filter: invert(0.5)"
                                     src="{% static 'Core/img/signout.png' %}" alt="signout">
                            </a>
                        </div>
                    {% else %}
                        <div class="nav-item my-auto">
                            <a class="btn btn-secondary py-0"
                               href="{% url 'signin' %}">
                                Sign In
                            </a>
                        </div>
                        <div class="nav-item my-auto">
                            <a class="btn btn-secondary py-0"
                               href="{% url 'signup' %}">
                                Sing Up
                            </a>
                        </div>
                    {% endif %}
                    <div class="nav-item">
                        <img width="25" height="25"
                             id="btn-change-theme"
                             src="{% static 'Core/img/moon.png' %}" alt="theme">
                    </div>
                </li>
            </ul>
        </div>
        ...
    </header>
    ```
    * ### Сейчас страница с фильмами должна выглядеть как-то так:
        ![](imgs/1.png)

2. ## Пишем карточку
    Скачайте `addon` к `bootstrap` для более быстрой верстки
    **[wide-classes](https://artasov.github.io/wide-classes/)**.<br>
    Переместите `wide-classes.css` в `Core/static/Core/css/` и подключите в `base.html`    
    
    > Естественно вы можете использовать просто классы bootstrap.
    Но согласитесь надпись <br>
    `d-flex flex-column justify-content-center align-items-center gap-2`<br>
    длиннее чем<br>
    `fccc gap-2`<br>
    Главное объяснить по какой логике строятся подобные классы.

    ```html
    <link type="text/css" rel="stylesheet"
          href="{% static 'Core/css/wide-classes.css' %}"/>
    ```
    ```html
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | Фильмы{% endblock %}
    {% block content %}
        <h1 class="text-center mb-4">Фильмы</h1>
        <div class="frc flex-wrap gap-4 mw-1000px mx-auto">
            {% for movie in movies %}
                <!-- Вспомните как формируются динамические маршруты в шаблонах -->
                <a href="{% url 'movie_detail' movie_id=movie.id %}" 
                   class="fc mw-300px w-100 text-light text-decoration-none hover-scale-2">
                    <img src="{{ movie.poster.url }}" alt="">
                    <h3 class="mt-2">{{ movie.title }}</h3>
                    <span class="frsc gap-2">
                        <span>Рейтинг:</span>
                        <span class="fs-5" style="color: #ffe655; padding-bottom: 1px;">
                            {{ movie.rating }}
                        </span>
                    </span>
                    <p>
                        {% for genre in movie.genres.all %}
                            {{ genre.name }}{% if not forloop.last %}, {% endif %}
                        {% endfor %}
                    </p>
                    <span class="text-secondary mt-auto">{{ movie.release_date }}</span>
                </a>
            {% endfor %}
        </div>
    {% endblock %}
    ```
    Чтобы дата отображалась на русском установите русский язык в `settings.py`
    ```python
    # settings.py
    LANGUAGE_CODE = 'ru-RU'
    ```
    Так как карточки фильмов будут не только на этой странице, дабы не
    копипастить код, вынесем карточку в отдельный шаблон.
    ```html
    <!-- kinopoisk/templates/kinopoisk/includes/movie_card.html -->
    <a href="{% url 'movie_detail' movie_id=movie.id %}" 
       class="fc mw-300px w-100 text-light text-decoration-none hover-scale-2">
        <img src="{{ movie.poster.url }}" alt="">
        ...
    </a>
    ```
    И используем его.
    ```html
    {% extends 'Core/base.html' %}
    {% block title %}Кинопоиск | Фильмы{% endblock %}
    {% block content %}
        <h1 class="text-center mb-4">Фильмы</h1>
        <div class="frc flex-wrap gap-4 mw-1000px mx-auto">
            {% for movie in movies %}
                <!-- Передаем переменную с объектом фильма, это не обязательно, 
                     но в дальнейшем мы будем это использовать, поэтому лучше сделать -->
                {% include 'kinopoisk/includes/movie_card.html' with movie=movie %}
            {% endfor %}
        </div>
    {% endblock %}
    ```

## Должно получиться примерно так.
![](imgs/img.png)
## Загрузите проект на гит если еще не загружали.

## Подведите итоги.
># git push...
