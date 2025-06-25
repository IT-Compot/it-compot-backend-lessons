# 📌 Урок: Генератор случайных шуток

## 📅 План на урок

✅ Создание модели `Joke`
✅ Создание представления для отображения случайной шутки
✅ Создание шаблона с кнопкой "Показать шутку"

---

# 📌 Что такое генератор случайных шуток?

Это простое Django-приложение, которое показывает случайную шутку из базы данных. Ученик узнаёт, как:

* создавать модели и миграции
* выбирать случайный объект из базы данных
* создавать представление и шаблон
* использовать кнопку для перезагрузки страницы

---

## 📦 Создание модели `Joke`

В файле `jokes/models.py` создаём модель:

```python
from django.db import models

class Joke(models.Model):
    text = models.TextField()

    def __str__(self):
        return self.text[:50]  # Показываем первые 50 символов шутки
```

После этого нужно выполнить миграции:

```sh
python manage.py makemigrations
python manage.py migrate
```

---

## 🧠 Добавление шуток в админку

Чтобы добавлять шутки через административную панель, зарегистрируем модель с помощью декоратора:

```python
# jokes/admin.py
from django.contrib import admin
from .models import Joke

@admin.register(Joke)
class JokeAdmin(admin.ModelAdmin):
    list_display = ('text',)
```

---

## 🔄 Вывод случайной шутки

Теперь создадим представление, которое будет показывать случайную шутку. Сделаем это максимально понятно:

```python
# jokes/views.py
import random
from django.shortcuts import render
from .models import Joke

def random_joke(request):
    # Получаем все шутки из базы данных
    all_jokes = Joke.objects.all()

    # Если в базе есть хотя бы одна шутка — выбираем случайную
    if len(all_jokes) > 0:
        joke = random.choice(all_jokes)
    else:
        joke = None

    # Отправляем шутку (или None) в шаблон
    return render(request, 'jokes/random_joke.html', {'joke': joke})
```

---

## 🌐 Подключение URL

```python
# jokes/urls.py
from django.urls import path
from .views import random_joke

urlpatterns = [
    path('', random_joke, name='random_joke'),
]
```

В главном файле `urls.py` подключим приложение:

```python
# project/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('jokes.urls')),
]
```

---

## 🖼️ Создание шаблона

Создаём файл `jokes/templates/jokes/random_joke.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Случайная шутка</title>
</head>
<body>
    <h1>Генератор шуток</h1>

    {% if joke %}
        <p>{{ joke.text }}</p>
    {% else %}
        <p>Шутки пока не добавлены.</p>
    {% endif %}

    <form method="get">
        <button type="submit">Показать другую шутку</button>
    </form>
</body>
</html>
```

---

## ❗ Возможные ошибки и решения

| Проблема                                              | Решение                                                |
| ----------------------------------------------------- | ------------------------------------------------------ |
| 🔴 `IndexError: Cannot choose from an empty sequence` | Убедитесь, что в базе данных есть хотя бы одна шутка   |
| 🔴 `TemplateDoesNotExist`                             | Проверьте путь к шаблону и наличие папки `templates`   |
| 🔴 `No module named 'jokes'`                          | Убедитесь, что приложение добавлено в `INSTALLED_APPS` |

---

## ✅ Заключение

На этом уроке мы научились:

* Создавать и использовать модели
* Отображать случайные записи из базы
* Работать с шаблонами и кнопками

🎉 **Урок завершён!**
