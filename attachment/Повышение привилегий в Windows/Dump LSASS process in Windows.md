Подключаемся к машине через xfreerdp, создаем дамп lsass с помощью Task manager -> lsass.exe -> Create dump file
Поднимаем smb server и передаем lsass.DMP на свою машину
Используем ```pypykatz lsa minidump lsass.DMP``` и получаем NT хеш пароля администратора
С помощью полученного хеша подключается через rdp и забираем флаг