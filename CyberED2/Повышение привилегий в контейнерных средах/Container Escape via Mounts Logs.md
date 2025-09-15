
в /opt/log_collector создаем симлинк ln -s /root  /opt/logcollector/root.log и удаляем info.log
после отработки скрипта появится архив archive_root.log..... в котором будет флаг
выведем содержание архива tar -xzf /opt/logcollector/archive_*.tar.gz -O