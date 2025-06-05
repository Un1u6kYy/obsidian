
на веб странице видим версию ПО **WebLogic Server Version: 12.2.1.3.0**
находим CVE-2020-14882 которая отправляет get запрос и обходим аутентификацию. Находим PoC (https://github.com/chacka0101/exploits/blob/master/CVE-2020-14882/CVE-2020-14882.py) с помощью которого получаем shell
забираем флаг из $FLAG
![](../../attachment/Pasted%20image%2020250512122912.png)