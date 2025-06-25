# 📌 Урок: Погода по API в Django

## 📅 План на урок

✅ Форма для ввода города
✅ Получение координат через OpenWeather API
✅ Получение погоды по координатам
✅ Отображение температуры, погоды и ветра

---

# 📌 Что мы делаем?

Сегодня мы создадим Django-приложение, которое показывает погоду. Ученик вводит город, а мы по API получаем прогноз и выводим:

* Температуру
* Описание погоды (ясно, дождь и т.п.)
* Скорость ветра

---

## ⚙️ Настройка API

Мы будем использовать сервис [OpenWeatherMap](https://openweathermap.org/api). Нужно заранее зарегистрироваться и получить `API_KEY`.

```python
API_KEY = 'your_api_key_here'  # Вставьте ваш ключ
```

---

## 🔄 Представление `weather_view`

```python
# weather/views.py
import requests
from django.shortcuts import render

API_KEY = 'your_api_key_here'  # замените на свой ключ

def weather_view(request):
    result = None

    if request.method == 'POST':
        try:
            # Получаем название города из формы
            city = request.POST.get('city')

            # 1. Получаем координаты города
            geo_url = f'http://api.openweathermap.org/geo/1.0/direct?q={city}&limit=5&appid={API_KEY}'
            geo_response = requests.get(geo_url).json()

            # Берём широту и долготу из первого подходящего результата
            lat = geo_response[0]['lat']
            lon = geo_response[0]['lon']

            # 2. Получаем данные о погоде
            weather_url = f'https://api.openweathermap.org/data/3.0/onecall?lat={lat}&lon={lon}&units=metric&exclude=current&appid={API_KEY}'
            weather_response = requests.get(weather_url).json()

            temperature = weather_response['hourly'][0]['temp']
            condition = weather_response['hourly'][0]['weather'][0]['main']
            wind_speed = weather_response['hourly'][0]['wind_speed']

            result = {
                'temperature': temperature,
                'condition': condition,
                'wind': wind_speed
            }

        except Exception:
            result = 'Ошибка! Возможно, введён неверный город.'

    return render(request, 'weather/weather.html', {'result': result})
```

---

## 🌐 Подключение URL

```python
# weather/urls.py
from django.urls import path
from .views import weather_view

urlpatterns = [
    path('', weather_view, name='weather'),
]
```

```python
# project/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('weather.urls')),
]
```

---

## 🖼️ Шаблон `weather.html`

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Погода</title>
</head>
<body>
    <h1>Узнать погоду в своём городе</h1>

    <form method="post">
        {% csrf_token %}
        <input type="text" name="city" placeholder="Введите город">
        <button type="submit">Узнать погоду</button>
    </form>

    {% if result %}
        {% if result == 'Ошибка! Возможно, введён неверный город.' %}
            <p style="color: red;">{{ result }}</p>
        {% else %}
            <p>🌡 Температура: {{ result.temperature }}°C</p>
            <p>☁️ Погода: {{ result.condition }}</p>
            <p>💨 Ветер: {{ result.wind }} м/с</p>
        {% endif %}
    {% endif %}
</body>
</html>
```

---

## ✅ Заключение

На этом уроке мы:

* Создали форму для города
* Получили координаты и прогноз
* Отобразили данные в шаблоне

🎉 Теперь вы умеете работать с внешними API в Django!
