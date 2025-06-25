# 📌 Урок: Квиз с одним вопросом

Привет! Сегодня мы вместе создадим мини-квиз на Django, в котором будет один случайный вопрос с четырьмя вариантами ответа. Вы сможете выбрать один из них, отправить свой выбор, и мы сразу скажем — правильно это или нет. Заодно разберём, как устроены модели, шаблоны, представления и формы в Django. Поехали!

## 📅 План на урок

✅ Создание модели `Question`
✅ Отображение вопроса и вариантов ответа
✅ Проверка правильности ответа

---

# 📌 Что такое квиз?

Квиз (викторина) — это приложение, которое показывает пользователю вопрос с несколькими вариантами ответа и проверяет, какой вариант он выбрал. Мы научимся:

* создавать модели с несколькими полями
* обрабатывать данные из форм (POST-запрос)
* сравнивать ответы и выводить результат

---

## 📦 Создание модели `Question`

В файле `quiz/models.py` создаём модель с вопросом, вариантами и правильным ответом:

```python
# Импортируем нужный модуль
from django.db import models

# Модель для хранения вопроса и вариантов ответов
class Question(models.Model):
    text = models.CharField(max_length=255)  # Текст вопроса
    option_a = models.CharField(max_length=100)  # Вариант A
    option_b = models.CharField(max_length=100)  # Вариант B
    option_c = models.CharField(max_length=100)  # Вариант C
    option_d = models.CharField(max_length=100)  # Вариант D

    # Поле для правильного ответа. Используем список возможных значений — A, B, C, D
    correct_option = models.CharField(
        max_length=1,
        choices=[('A', 'A'), ('B', 'B'), ('C', 'C'), ('D', 'D')]
    )

    def __str__(self):
        return self.text  # Показываем сам вопрос в админке
```

После этого нужно выполнить миграции:

```sh
python manage.py makemigrations
python manage.py migrate
```

---

## 🧠 Добавление вопросов через админку

Чтобы добавлять вопросы через административную панель:

```python
# quiz/admin.py
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

---

## 🔄 Представление для отображения и обработки ответа

Сейчас мы напишем код, который будет показывать случайный вопрос и проверять ответ пользователя. Давайте разберём его пошагово, чтобы всё было понятно.

```python
# Импортируем нужные модули
import random
from django.shortcuts import render
from .models import Question

# Главное представление нашего квиза
def quiz_view(request):
    # Получаем все вопросы из базы данных
    all_questions = Question.objects.all()

    # Проверяем, есть ли хотя бы один вопрос
    if len(all_questions) > 0:
        # Если есть — выбираем случайный
        question = random.choice(all_questions)
    else:
        # Если вопросов нет — показывать нечего
        question = None

    result = None  # Здесь мы будем хранить True или False — правильный ответ или нет

    # request — это объект, который приходит от пользователя.
    # В нём хранится информация: что отправлено, каким методом (GET или POST), какие данные.

    # Если пользователь нажал кнопку "Ответить" — это будет POST-запрос
    if request.method == 'POST':
        # Получаем ответ пользователя из формы
        selected = request.POST.get('answer')

        # А это правильный ответ, который мы передали в скрытом поле
        correct = request.POST.get('correct')

        # Сравниваем: совпадает ли выбранный вариант с правильным
        if selected == correct:
            result = True
        else:
            result = False

    # Передаём всё в шаблон: сам вопрос и результат (если уже ответили)
    return render(request, 'quiz/quiz.html', {
        'question': question,
        'result': result
    })
```

> 🔹 Обратите внимание: мы используем скрытое поле `correct`, чтобы сохранить правильный ответ прямо в форме. Это простой способ, но в более серьёзных проектах лучше брать правильный ответ из базы по ID вопроса — так безопаснее.

Теперь можно переходить к созданию шаблона!

---

## 🌐 Подключение URL

```python
# quiz/urls.py
from django.urls import path
from .views import quiz_view

urlpatterns = [
    path('', quiz_view, name='quiz'),
]
```

Подключим приложение в главном `urls.py`:

```python
# project/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('quiz.urls')),
]
```

---

## 🖼️ Создание шаблона

Создаём файл `quiz/templates/quiz/quiz.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Квиз</title>
</head>
<body>
    <h1>Вопрос дня</h1>

    {% if question %}
        <form method="post">
            {% csrf_token %}
            <p>{{ question.text }}</p>

            <label><input type="radio" name="answer" value="A"> {{ question.option_a }}</label><br>
            <label><input type="radio" name="answer" value="B"> {{ question.option_b }}</label><br>
            <label><input type="radio" name="answer" value="C"> {{ question.option_c }}</label><br>
            <label><input type="radio" name="answer" value="D"> {{ question.option_d }}</label><br>

            <input type="hidden" name="correct" value="{{ question.correct_option }}">
            <button type="submit">Ответить</button>
        </form>

        {% if result is not None %}
            {% if result %}
                <p><strong>Правильно! 🎉</strong></p>
            {% else %}
                <p><strong>Неправильно 😞</strong></p>
            {% endif %}
        {% endif %}

    {% else %}
        <p>Вопросы пока не добавлены.</p>
    {% endif %}
</body>
</html>
```

---

## ❗ Возможные ошибки и решения

| Проблема                                              | Решение                                                |
| ----------------------------------------------------- | ------------------------------------------------------ |
| 🔴 `IndexError: Cannot choose from an empty sequence` | Добавьте хотя бы один вопрос в базу данных             |
| 🔴 `TemplateDoesNotExist`                             | Проверьте путь к шаблону и структуру папок             |
| 🔴 `No module named 'quiz'`                           | Убедитесь, что приложение добавлено в `INSTALLED_APPS` |

---

## ✅ Заключение

На этом уроке мы научились:

* создавать и заполнять модель с вопросами и вариантами
* выводить вопрос и собирать ответы
* проверять, правильный ли выбран вариант

🎉 **Урок завершён!**
