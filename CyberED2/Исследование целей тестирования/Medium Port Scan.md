сканируем сеть 192.168.0.100/23 
![](../../attachment/Pasted%20image%2020250319202607.png)

curl http://192.168.0.10 получаем первую часть флага

nmap 192.168.0.230 -p- --open -Pn 
подключаемся к redis
nc -vn 192.168.0.230 6379
проверим ключи keys *
get flag
получаем третью часть флага  
![](../../attachment/Pasted%20image%2020250319211619.png)

Подключаемся к порту и проверяем. Видим сайт и получаем 4 часть флага
![](../../attachment/Pasted%20image%2020250319211831.png)

запускаем udp сканирование
nmap 192.168.0.113 -p- --open -sU --max-retries 1 --min-rate 5 -T4
nc -vnu 192.168.0.113 670
Забираем вторую часть флага
