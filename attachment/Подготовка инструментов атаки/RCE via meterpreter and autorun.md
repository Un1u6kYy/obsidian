
Создаем файл elf через msfvenom
Запускаем слушателя и закидываем файл на веб сайт
После получения шелла создаем файл /etc/rc.local и прописывать туда echo "/bin/bash /var/www/html/uploads/processed/payload.elf"
забираем флаг в папке /tmp 