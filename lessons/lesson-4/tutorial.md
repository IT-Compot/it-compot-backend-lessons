# Магическое число 2
## Мы продолжаем делать нашу мини игру - угадай число!

* ### Генерируем число и сравниваем его со случайным. Отправляем результат в шаблон.
    ```python
    # Core/views.py
    import random
    def magic_number(request):
        # Получаем данные из request
        number = request.POST['number']
        # Преобразуем строку в число
        number = int(number)
        # Генерируем случайное число от 1 до 5
        random_number = random.randint(1, 5)
        
        if number == random_number:
            result = "Поздравляем, вы угадали число!"
        else:
            result = f"К сожалению, было загаданно число {random_number}. Попробуйте ещё раз."
            
        return render(request, 'Core/magic_number.html', {'result': number})
    ```
* ### Исправляем возникшие проблемы.
    Теперь без `post` запроса мы не можем загрузить страницу из-за ошибки.<br>
    Знакомим немного с тем как отображаются ошибки django.<br>
    Когда мы просто загружаем страницу мы выполняем `GET`, <br>
    а обрабатываем корректно только `POST`.<br>
    Нужно отдельно обрабатывать эти запросы.
    Значит нужно сделать проверку `if request.method == 'POST':`<br>
    > Приучаем к правильной терминологии.
    Поле method объекта request содержит строку, обозначающую тип запроса: 'POST', 'PUT', 'GET', ...

    Даем время подумать куда вставить подобную проверку и как правильно организовать желаемый код.<br>
    ```python
    # Core/views.py
    from django.shortcuts import render
    import random
    
    def magic_number(request):
        # Если POST
        if request.method == 'POST':
            number = int(request.POST['number'])
            random_number = random.randint(1, 5)
            if number == random_number:
                result = "Поздравляем, вы угадали число!"
            else:
                result = f"К сожалению, загаданное число было {random_number}. Попробуйте ещё раз."
            
            return render(request, 'magic_number.html', {'result': result})
        
        # Если GET
        return render(request, 'magic_number.html')
    ```
    
* ### Проверяем, радуемся.
* ## ВНИМАНИЕ !
    **На этом этапе очень важно чтобы ученики поняли, что есть разные виды 
    запросов, как минимум `GET` и `POST`, есть маршрут(`url`), есть `контроллер`, 
    есть визуал(`template`), и все это вместе дает возможность гибко 
    управлять поведением сайта и его страниц.**

* ### Если осталось время, можно улучшить код вот так:
    Будет отправлять в шаблон `True` или `False`, а в шаблоне 
    отобразим желаемые надписи разными цветами.
    > Использование условий в шаблонах описано в шпаргалке
    ```html
    <!-- Core/magic_number.html -->
    <!-- Можно просто {% if result %}, но так менее наглядно пока что -->
    {% if result == True %} 
    <p class="text-success fw-bold">Поздравляем, вы угадали число!</p>
    {% else %}
    <p class="text-danger fw-bold">К сожалению вы не угадали число :(</p>
    {% endif %}
    ```
    ```python
    # Core/views.py
    from django.shortcuts import render
    import random
    
    def magic_number(request):
        if request.method == 'POST':
            number = int(request.POST['number'])
            random_number = random.randint(1, 5)
            if number == random_number:
                result = True
            else:
                result = False
            return render(request, 'magic_number.html', {'result': result})
        return render(request, 'magic_number.html')
    ```
    Или еще короче:
    ```python
    # Core/views.py
    def magic_number(request):
        if request.method == 'POST':
            return render(request, 'magic_number.html', {
                'result': int(request.POST['number']) == random.randint(1, 5)
            })
        return render(request, 'magic_number.html')
    ```

## Подведите итоги.
