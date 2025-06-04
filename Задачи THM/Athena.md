При сканирование видим открытый 22,80,139,445 порт

![](../attachment/Pasted%20image%2020250603101600.png)

с помощью impacket-smbclient можем подключиться к шаре и найдет сообщение для администратора 
![](../attachment/Pasted%20image%2020250603102125.png)

на сайте находим возможность CI и получаем шелл
![](../attachment/Pasted%20image%2020250603105138.png)
![](../attachment/Pasted%20image%2020250603105939.png)

изучив систем находим скрипт backup.sh. С помощью vim/echo удаляем/добавляем обратный шелл через mkfinfo

![](../attachment/Pasted%20image%2020250603111503.png)
![](../attachment/Pasted%20image%2020250603112748.png)
![](../attachment/Pasted%20image%2020250603112733.png)

Получаем первый флаг юзера
![](../attachment/Pasted%20image%2020250603112832.png)

Изучив систему (sudo -l) находим файл venom.ko (руткит [GitHub - m0nad/Diamorphine: LKM rootkit for Linux Kernels 2.6.x/3.x/4.x/5.x/6.x (x86/x86_64 and ARM64)](https://github.com/m0nad/Diamorphine)) и получает рут права
![](../attachment/Pasted%20image%2020250603113634.png)
![](../attachment/Pasted%20image%2020250603113751.png)