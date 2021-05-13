# Highload_Mail_project
## Тема и целевая аудитория
Тема: проектирование высоконагруженного сервиса электронной почты, позволяющего пользователям отправлять и получать письма, которые могут содержать вложения.

Возможности, которые предоставляет пользователю проектируемый сервис:
- Регистрация
- Авторизация
- Отправка/пересылка писем (письма могут включать вложения до 15 мб)
- Чтение писем
- Постраничное получение списка последних писем из почтового ящика
- Удаление писем

Согласно [radar.yandex](https://radar.yandex.ru/yandex?month=2021-04), месячная аудитория Яндекс.Почты составляет 16 млн пользователей, а дневная 5.5 млн пользователей. По данным [similarweb](https://www.similarweb.com/website/mail.yandex.ru/#overview), каждый пользователь проводит в среднем по 8 минут на сайте.

На основании этих данных обозначим целевые цифры проекта:

- Эжемесячная аудитория проекта - 15 млн пользователей
- Количество активных аккаунтов - 50 млн (~35% населения России)
- Сервис будет направлен на Российскую аудиторию.

## Расчет нагрузки
Определим данные среднего пользователя почтового сервиса.
Пусть пользователь отправляет 2 письма в день, получает 10 писам в день и проверяет почту в среднем 5 раз в день.
