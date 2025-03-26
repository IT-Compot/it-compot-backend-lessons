# Кинопоиск. Адреса и страницы

> 🧭 **Комментарий для преподавателя:** На этом этапе важно показать ученикам, как правильно организовывать маршруты и представления, не усложняя логику. Сделайте упор на понимание структуры: какие бывают пути, зачем нужны динамические параметры, и как Django подставляет значения в функции.

---

## 1. Добавьте таблицы в админку

```python
# kinopoisk/admin.py
from django.contrib import admin
from .models import MoviePerson, Genre, Movie, MovieReview

@admin.register(MoviePerson)
class MoviePersonAdmin(admin.ModelAdmin):
    list_display = ('name', 'photo', 'birth_date', 'role')

@admin.register(Genre)
class GenreAdmin(admin.ModelAdmin):
    list_display = ('name',)

@admin.register(Movie)
class MovieAdmin(admin.ModelAdmin):
    list_display = ('title',  'poster', 'release_date', 'rating', 'duration')

@admin.register(MovieReview)
class MovieReviewAdmin(admin.ModelAdmin):
    list_display = ('movie', 'author', 'created_at', 'likes')
```

---

## 2. Создайте суперюзера и проверьте, что таблицы появились в админке

> 🔐 **Подсказка:** superuser создается командой `python manage.py createsuperuser`. Убедитесь, что в админке отображаются все четыре модели. Если чего-то нет — значит не зарегистрировали.

---

Продолжаем проектировать `Кинопоиск`, и сегодня мы напишем **все нужные маршруты** и создадим контроллеры для них.

> ⚠️ **Внимание:** Всё, что мы делаем — вы уже делали с учениками. Ваша задача — не делать за них, а направлять их к верным выводам. Давайте им вопросы, подводите к мыслям.

---

## 3. Подготовка

> 🛠 **Объяснение:** `ROOT_URLCONF` указывает, какой файл Django считает главным для маршрутов. Мы заменим его на `Core.urls`, потому что теперь всё будет идти через наше приложение `Core`.

Замените:
```python
ROOT_URLCONF = 'config.urls'
```
на:
```python
ROOT_URLCONF = 'Core.urls'
```

Удалите `config.urls`, а в `Core/urls.py` подключите маршруты из приложения `kinopoisk`.

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
    path('', include('kinopoisk.urls')),  # Подключаем маршруты из приложения kinopoisk
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

> 📦 **Пояснение:** MediaFiles — это картинки, аватары и т.п. Настройка нужна, чтобы при `DEBUG=True` они открывались правильно. Смотри шпаргалку.

---

## 4. Пишем маршруты

Создайте файл `kinopoisk/urls.py`

```python
from django.urls import path
from .views import *

urlpatterns = [
    path('', main, name='main'),  # Главная страница
    path('movies/', movie_list, name='movie_list'),
    path('actors/', actor_list, name='actor_list'),
    path('directors/', director_list, name='director_list'),
    path('genres/', genre_list, name='genre_list'),

    path('movie/<int:movie_id>/', movie_detail, name='movie_detail'),
    path('actor/<int:actor_id>/', actor_detail, name='actor_detail'),
    path('director/<int:director_id>/', director_detail, name='director_detail'),
    path('genre/<int:genre_id>/', genre_detail, name='genre_detail'),
]
```

> 🔍 **Объяснение:** `<int:movie_id>` — это динамический параметр. Django сам подставит значение, если пользователь зайдёт по адресу вроде `/movie/12/` и передаст его в функцию `movie_detail`.

---

## 5. Создаём шаблоны

> 🎭 **Совет:** Не плодите шаблоны без нужды. У актёров и режиссёров будет один шаблон, потому что данные у них похожие. Главное — показать, что HTML может быть универсальным.

Итого:
- маршрутов: **9**
- шаблонов: **7**

```text
kinopoisk/templates/kinopoisk/main.html
kinopoisk/templates/kinopoisk/movie_list.html
kinopoisk/templates/kinopoisk/person_list.html
kinopoisk/templates/kinopoisk/genre_list.html

kinopoisk/templates/kinopoisk/movie_detail.html
kinopoisk/templates/kinopoisk/person_detail.html
kinopoisk/templates/kinopoisk/genre_detail.html
```

> 📁 **Подсказка:** Убедитесь, что папка `templates/kinopoisk/` лежит внутри приложения и путь в `settings.py` настроен верно.

---

## 6. Пример контроллеров (views)

> 📣 **Подсказка:** Здесь пока просто заглушки. Мы потом добавим в них данные из моделей.

```python
# kinopoisk/views.py
from django.shortcuts import render

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

> 🤔 **Объяснение:** Параметры `movie_id`, `actor_id` и т.д. будут использоваться позже, когда будем доставать информацию из базы. Сейчас — просто отображение шаблонов.

---

## Подведите итоги

✅ Мы:
- Зарегистрировали модели в админке
- Подключили и настроили маршруты
- Создали шаблоны
- Написали заглушки для представлений

> 💬 **GitHub потом.** Или сейчас, если успеваете. Главное — сделать это до 3-го занятия с Кинопоиском.

Если хочешь, могу добавить картинку со схемой маршрутов или визуально показать, как адреса связаны с шаблонами. Готова продолжать! 🧩

