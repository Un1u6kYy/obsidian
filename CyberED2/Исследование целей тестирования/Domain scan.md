Запускаем gobuster dns -d company.local -w SecLists/Discovery/DNS/subdomains-top1million-110000.txt 
![](../../attachment/Pasted%20image%2020250319181200.png)
находим адрес dig ldaptest.company.local
nmap => curl http://ldaptest.company.local