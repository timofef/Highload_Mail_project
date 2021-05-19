# Highload_Mail_project
## Тема и целевая аудитория
Тема: проектирование высоконагруженного сервиса электронной почты, позволяющего пользователям отправлять и получать письма, которые могут содержать вложения.

Возможности, которые предоставляет пользователю проектируемый сервис:
- Регистрация и авторизация
- Отправка/пересылка писем (письма могут содержать вложения)
- Чтение писем
- Постраничное получение списка последних писем из почтового ящика (последние 50)
- Удаление писем

Согласно [radar.yandex](https://radar.yandex.ru/yandex?month=2021-04), в 2021 году месячная аудитория Яндекс.Почты составляет 16 млн пользователей, а дневная 5.5 млн пользователей.

На основании этих данных обозначим целевые цифры проекта:

- Дневная аудитория проекта - 5.5 млн пользователей
- Количество активных аккаунтов - 50 млн (~35% населения России)
- Сервис будет направлен на Российскую аудиторию.

## Расчет нагрузки
Определим данные среднего пользователя почтового сервиса.

Пусть средний пользователь отправляет 3 письма в день, получает 10 писем в день и проверяет почту в среднем 5 раз в день. По данным Яндекса, средний размер письма без вложений - 4 кб, с вложениями - 400 кб. При этом только 10% писем включают в себя вложения.

#### RPS по типам запросов:
1. Авторизация:

    Так средний пользователь проверяет почту 5 раз в день (то есть заходит на сайт), то и количество запросов на авторизацию от одного пользователя будет равно 5:
  
    ```RPS = 5.5 * 10^6 * 5 / (24 * 60 * 60) ~ 320```

2. Отправка писем:

    Если считать, что средний пользователь отправляет 3 письма в день, то количество запросов на отправку письма в секунду:
   
    ```RPS = 5.5 * 10^6 * 3 / (24 * 60 * 60) ~ 190```

3. Чтение писем:

    Предполагая, что пользователь читает все полученные за день письма (10 писем):
    
    ```RPS = 5.5 * 10^6 * 10 / (24 * 60 * 60) ~ 636```

4. Постраничное получение списка последних писем из почтового ящика:

    Считая, что пользователь проверяет почту 5 раз в день, получим:
    
    ```RPS = 5.5 * 10^6 * 5 / (24 * 60 * 60) ~ 320```
    
5. Удаление писем:

    Так как по [статистике](https://www.statista.com/statistics/420400/spam-email-traffic-share-annual/) 28% писем являются спамом, то в среднем пользователь будет удалять 3 письма в день:
        
    ```RPS = 5.5 * 10^6 * 3 / (24 * 60 * 60) ~ 190```
    
Итого, получаем среднее количество запросов с секунду: ```RPS ~= 1660```

Рассчитаем траффик, генерируемый пользователями. Пусть в момент пиковой нагрузки сайтом пользуется 70% суточной аудитории:

```Кол-во пользователей при пиковой нагрузке = 5.5 * 10^6 * 0.7 ~ 3.85 * 10^6```

#### Траффик для различных типов запросов:

1. Отправка писем:

    Средний RPS для запросов на отправку писем равен 190. 90% из них не содержат вложений и весят в среднем по 4 кб, остальные 10% имеют вложения и весят в среднем 400 кб. Тогда:
    ```
    Траффик: (190 писем/сек) * (0.9 * 4 * 2^13 + 0.1 * 400 * 2^13) ~ 68 Мбит/c
    Пиковый траффик: (3.85 * 10^6 пользователей) * (0.9 * 4 * 2^13 + 0.1 * 400 * 2^13) ~ 1375 Гбит/c
    ```
    
2. Чтение писем:

    RPS для чтения писем равен 636. Замеры ([1](read_message1.png), [2](read_message2.png), [3](read_message3.png), [4](read_message4.png), [5](read_message5.png)) показали, что запрос на чтение одного письма весит в среднем 9.6 кб.
    ```
    Траффик: (636 запросов/сек) * 9.6 * 2^13 ~ 50 Мбит/c
    Пиковый траффик: (3.85 * 10^6 пользователей) * 9.6 * 2^13 ~ 303 Гбит/c
    ```
    
4. Постраничное получение списка последних писем из почтового ящика:

    [Замер](list50.png) показал, что запрос на полчение списка из последних 50 писем весит 29 кб.
    ```
    Траффик: (320 запросов/сек) * 29 * 2^13 ~ 76 Мбит/c
    Пиковый траффик: (3.85 * 10^6 пользователей) * 29 * 2^13 ~ 915 Гбит/c
    ```
    Средний суточный траффик: ```(68 + 50 + 76) * 24 * 60 * 60 ~ 1905 Гбайт/сутки```
    
## База данных

### Логическая схема базы данных
База данных будет включать следующие основные сущности:

- Пользователь
- Письмо
- Вложение

![](logic.png)
