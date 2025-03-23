# Шаблонизация в зависимости от аутентифицированности и права доступа 

Вспомним, что в предыдущий раз мы сделали 4 кнопки в header
`signup`, `signin`, `profile`, `signout`. 😊<br>

Очевидно, что нам нужно видеть `profile` и `signout` только когда мы **_вошли_**.<br>
А `signup` и `signin` только когда **_не вошли_**.

Вспомним, что мы передаем объект `request` в функцию `render`. 🚀
```python
return render(request, 'example.html')
```
Объект `request` содержит информацию о текущей 
сессии и аутентифицированном пользователе(и не только). Это позволяет 
отображать пользовательские данные и изменять содержимое 
страницы в зависимости от состояния пользователя.
Мы можем использовать его в шаблоне как обычную переменную и выводить разные поля этого объекта. 🔍
```html
<!-- В вашем шаблоне (template.html) -->
<!-- Можете ради интереса все это вывести и посмотреть на настоящие данные -->
<p>{{ request.method }}</p>
<p>{{ request.GET }}</p>
<p>{{ request.POST }}</p>
<p>{{ request.COOKIES }}</p>
<p>{{ request.session }}</p>
<p>{{ request.user }}</p>
<p>{{ request.user.username }}</p>
<p>{{ request.user.first_name }}</p>
<p>{{ request.user.is_authenticated }}</p>
```
> Можете заскринить и кинуть ученикам почитать 😊

1. ## Исправим отображение ссылок в `header`. 🎯
   Напомните ученикам об [использовании условий в шаблонах](https://github.com/xlartas/it-compot-backend-methods/blob/main/django-base.md#%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D1%86%D0%B8%D0%BA%D0%BB%D0%BE%D0%B2-%D0%B8-%D1%83%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D0%B9-%D0%B2-%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD%D0%B5)
   и скажите, что `request.user.is_authenticated` возвращает
   `True`/`False`, пусть попробуют сами условно отображать ссылки.
   ```html
   <!-- Вот так -->
   {% if request.user.is_authenticated %} 
   
   {% else %}
   
   {% endif %}
   ```
   `{% if request.user.is_authenticated == True %}` для ученика понятнее. 
   ```html
    <header class="bg-primary text-white text-center py-4">
        <div class="container">
            <h1>{% block header_title %}Welcome{% endblock %}</h1>
            <p class="lead">{% block header_subtitle %}Shop with us!{% endblock %}</p>
        </div>
        <nav class="nav justify-content-center">
            {% if user.is_authenticated %}
                <a class="nav-link text-white" href="{% url 'signout' %}">Выход</a>
                <a class="nav-link text-white" href="{% url 'orders' %}">Ордерс</a>
            {% else %}
                <a class="nav-link text-white" href="{% url 'signup' %}">Регистрация</a>
                <a class="nav-link text-white" href="{% url 'signin' %}">Вход</a>
            {% endif %}
        </nav>
    </header>
   ```
   Проверьте, что все корректно отображается. ✅

2. ## Управление доступом и правами пользователей 🔑
   Сейчас после входа в аккаунт мы можем перейти на адреса `signup` и `signin`, 
   а если разлогинимся, то сможем перейти в профиль, что неправильно.<br>
   Мы можем проверять есть ли в сессии аутентифицированный пользователь и опираясь на
   это рендерить страницу или перенаправлять или еще что-то. 🚦
   
   ```python
   def example(request):                        
       if not request.user.is_authenticated:
           return redirect('login')
       return render(request, 'example.html')
   ```
   ### Применяем эти знания 🛠️
   > Ставьте `not` где нужно, и не ставьте, где не нужно
   ```python
   def signup(request):
    if request.user.is_authenticated:
        return redirect('orders')
    if request.method == 'POST':
        username = request.POST['username']
        email = request.POST['email']
        password = request.POST['password']
        r_password = request.POST['r_password']
        if password != r_password:
            return render(request, 'acounts/signup.html', {'error':'Пароли не совпадают'})
        User.objects.create_user(username=username, email=email, password=password)
        return redirect('signin')
    return render(request, 'acounts/signup.html')

   def signin(request):
       if request.user.is_authenticated:
           return redirect('orders')
       if request.method == 'POST':
           username = request.POST['username']
           password = request.POST['password']
           user = authenticate(request, username=username, password=password)
           if user:
               login(request,user)
               return redirect('orders')
           else:
               return render(request, 'acounts/signin.html', {'errors':'Неверные данные'})
       return render(request, 'acounts/signin.html')
   
   def signout(request):
       logout(request)
       return redirect('signin')
   ```

> Немного свободного времени должно остаться, чтобы догнать, если опаздывали. 😊

Можете пройтись по основам программирования на Python. 🐍

Можно рассказать, что существует множество пакетов, расширяющих возможности Django.<br>
Например, `django-allauth` – это мощная библиотека для Django, предназначенная для облегчения 
процессов аутентификации, регистрации и управления учетными записями пользователей. 
Она предоставляет интеграцию с социальными сетями и другими внешними провайдерами 
аутентификации, что позволяет пользователям регистрироваться и входить в систему с 
помощью своих учетных записей в этих сервисах 
(например, **Google**, **GitHub**, **Telegram**, **Vk**, **Twitter** и т.д.). 🚀
Расширенная обработка электронной почты, включая подтверждение электронной почты.

## Подведите итоги. 🏁
># git push...

