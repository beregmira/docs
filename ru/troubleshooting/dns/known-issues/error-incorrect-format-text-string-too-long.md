# Устранение ошибки Incorrect format. Text string too long


## Описание проблемы {#issue-description}

При вводе текстовой строки возникает ошибка вида:
```
Incorrect format. Text string too long
```

## Решение {#issue-resolution}

В TXT-записях строки длиннее 255 символов запрещены. Согласно RFC 4408, более длинную строку SPF/DKIM разбивают на несколько последовательных строк, а читающая сторона соединяет их в одну, например, как в этой [статье](https://kb.isc.org/docs/aa-00356).

Такую длинную запись можно разбить на две с помощью YC CLI командой: 
```
yc dns zone add-records <...> --record '<имя> ￼ TXT "v=DKIM1; k=rsa; p=..." "..."'
```

Такая TXT-запись успешно добавится.