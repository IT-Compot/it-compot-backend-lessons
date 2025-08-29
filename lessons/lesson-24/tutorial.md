# Кинопоиск. Адреса и страницы.

Продолжаем проектировать `Кинопоиск` и сегодня
мы напишем ВСЕ нужные нам маршруты и создадим контроллеры для них.

**Все что мы будем сегодня делать, мы уже делали, это довольно банальные вещи.
Просто направляйте учеников на правильные выводы, а они сами все должны сделать.**

1. ## Подготовка
    Первое, что нужно сделать это заменить `ROOT_URLCONF = 'config.urls'` на 
    `ROOT_URLCONF = 'Core.urls'`, удалить `config.urls`, а в `Core.urls` подключить 
    маршруты из приложения `kinopoisk`, которые кстати мы еще не создали.
    ```python
    # Core/urls.py
    from django.conf import settings
    from django.conf.urls.static import static
    from django.contrib import admin
    from django.urls import path, include
    
    from .views import signup, signin, profile, signout
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('signup/', signup, name='signup'),
        path('signin/', signin, name='signin'),
        path('signout/', signout, name='signout'),
        path('profile/', profile, name='profile'),
    
        path('', include('kinopoisk.urls')), <--------------
    ]
    if settings.DEBUG:
        urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    ```
    ### Вспомните про `MediaFiles`, что это такое и выполните их настройку.
    > В шпаргалке все есть.

2. ## Пишем маршруты.
    Создайте `kinopoisk/urls.py`
    ```python
    # kinopoisk/urls.
    from django.urls import path
    from .views import *
   
    urlpatterns = [
        path('', main, name='main'),  # Главная страница.
    
        path('movies/', movie_list, name='movie_list'),  # Список всех фильмов.
        path('actors/', actor_list, name='actor_list'),  # Список всех актеров.
        path('directors/', director_list, name='director_list'),  # Список всех режиссеров.
        path('genres/', genre_list, name='genre_list'),  # Список всех жанров.
    
        path('movie/<int:movie_id>/', movie_detail, name='movie_detail'),  # Детали фильма.
        path('actor/<int:actor_id>/', actor_detail, name='actor_detail'),  # Детали актера + его фильмы.
        path('director/<int:director_id>/', director_detail, name='director_detail'),  # Детали режиссера + его фильмы.
        path('genre/<int:genre_id>/', genre_detail, name='genre_detail'),  # Фильмы по жанру.
    ]
    ```
3. ## Создадим шаблоны
    Страница с режиссерами будет содержать информацию о режиссере и о его фильмах.
    Кстати то же самое будет и с актерами. А значит нам не нужно делать
    `actor_detail` и `director_detail`, достаточно `person_detail`.
    И то же самое со страницей списка актеров и страницей списка режиссеров, 
    понадобится лишь 1 шаблон.<br>
    Маршрутов 9, а шаблонов всего 7.
    ```javascript
    kinopoisk/tempaltes/kinopoisk/main.html.html
   
    kinopoisk/tempaltes/kinopoisk/movie_list.html
    kinopoisk/tempaltes/kinopoisk/person_list.html
    kinopoisk/tempaltes/kinopoisk/genre_list.html
   
    kinopoisk/tempaltes/kinopoisk/movie_detail.html
    kinopoisk/tempaltes/kinopoisk/person_detail.html
    kinopoisk/tempaltes/kinopoisk/genre_detail.html
    ```

4. ## Создадим примерный вид контроллеров.
    Тут важно не забыть про дополнительные аргументы из динамических маршрутов.
    ```python
    # kinopoisk/views.py
    def main(request):
        return render(request, 'kinopoisk/main.html')
    
    def movie_list(request):
        return render(request, 'kinopoisk/movie_list.html')
    
    def actor_list(request):
        return render(request, 'kinopoisk/person_list.html')
    
    def director_list(request):
        return render(request, 'kinopoisk/person_list.html')
    
    def genre_list(request):
        return render(request, 'kinopoisk/genre_list.html')
    
    def movie_detail(request, movie_id):
        return render(request, 'kinopoisk/movie_detail.html')
    
    def actor_detail(request, actor_id):
        return render(request, 'kinopoisk/person_detail.html')
    
    def director_detail(request, director_id):
        return render(request, 'kinopoisk/person_detail.html')
    
    def genre_detail(request, genre_id):
        return render(request, 'kinopoisk/genre_detail.html')
    ```

## Подведите итоги.
>### GitHub потом. Или сейчас если успеваете. Его нужно сделать за первые 3 занятия с кинопоиском.
