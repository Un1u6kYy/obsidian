находим wordpress на 80 порту
запускаем wpscan для поиска пользователей и возможных уязвимостей
```
wpscan --url http://10.10.69.51/wordpress  --enumerate u   --detection-mode aggressive -P ~/Downloads/seclists/Passwords/xato-net-10-million-passwords-1000000.txt --api-token XXX
```

![](../attachment/Pasted%20image%2020250409141406.png)

![](../attachment/Pasted%20image%2020250410091432.png)

Находим privilege escalation CVE-2023-1874 https://github.com/thomas-osgood/cve-2023-1874
Запускаем скрипт с ранее полученным паролей пользователя bob и получаем права администратора
![](../attachment/Pasted%20image%2020250409144651.png)

Переходим в управление темами и изменяем одну из страниц php на код для revshell
![](../attachment/Pasted%20image%2020250409150211.png)

![](../attachment/Pasted%20image%2020250409150324.png)


Запускаем linpeas и видим  возможную CVE-2022-0847
![](../attachment/Pasted%20image%2020250410091812.png)

Копируем скрипты и запускаем. Первый скрипт не справился, второй дал права root
Забираем 3 флага имея полный доступ

[AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits: A collection of exploits and documentation that can be used to exploit the Linux Dirty Pipe vulnerability.](https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits)
![](../attachment/Pasted%20image%2020250409172829.png)
![](../attachment/Pasted%20image%2020250409172947.png)
