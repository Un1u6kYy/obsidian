Изучаем машину на наличие шар и пользователей
![](../attachment/Pasted%20image%2020251010124005.png)Находим пользователя ArthurMorgan 

Исследуем директории сайта и найдем записку
![](../attachment/Pasted%20image%2020251010125505.png)
![](../attachment/Pasted%20image%2020251010125525.png)


Находим XPath Injection при авторизации на сайте и видим пароли и логины для пользователей
![](../attachment/Pasted%20image%2020251010130019.png)
![](../attachment/Pasted%20image%2020251010130558.png)
Помещаем web reverse shell через smbclient с помощью команды ``` put shell.php```и подключаемся к нему для получения доступа к системе или используем ssh для подключения
![](../attachment/Pasted%20image%2020251010130836.png)
![](../attachment/Pasted%20image%2020251010130916.png)
![](../attachment/Pasted%20image%2020251010131033.png)

Сменим пользователя на ArthurMorgan и видим что в переменном окружение открыт порт 4545
![](../attachment/Pasted%20image%2020251010132704.png)
При подключение к порту 4545 появляется выбор нескольких опций. При выборе 4 опции появляется окно vim  в котором мы можем выполнить :!/bin/bash и получить оболочку marston
![](../attachment/Pasted%20image%2020251010160321.png)Затем  с помощью команд
```
export TERM=xterm
tmux ls
tmux attach -t 0
<CTRL>b+n
```
необходимо найти терминал запущенный от пользователя root и получить флаг (но его нет:( )