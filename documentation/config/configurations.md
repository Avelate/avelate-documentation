# Configurations

Серверне ядро активно використовує змінні оточення, які описуються в конфігураційних файлах. Деталі організації та роботи сервісу конфігурацій дивись [ConfigurationService](../server-platform/services.md#configurationservice). 

Кожний конфігураційний файл має назву своєї області призначення та знаходиться в директорії `config`:

```
.
├── config
|   └── business.json
|   └── database.json
|   └── manager.json
|   └── ...
├── src
|   └── ...
|   └── adapters
|   └── factories
|   └── ...
|   └── server.ts
```

Повний перелік конфігураційних файлів та їх призначення:

## Конфігураційні файли та їх призначення

| Назва файлу | Опис |
| :---------: | :--- |
| business.json | Опис змінних, які стосуються бізнес-логіки |
| database.json | Опис змінних підключення бази данних |
| git.json | Опис змінних, які описують доступ до Git-репозиторія з бізнес-логікою |
| integration.json | Опис змінних інтеграцій |
| manager.json | Опис змінних менеджерів |
| measure.json | Опис змінних керування замірами продуктивності екземпляра серверної платформи |
| provider.json | Опис змінних, які стосуються провайдерів `providers` |
| memory.json | Опис змінних підключення `InMemory` глобального сховища  |
| server.json | Опис змінних, які стосуються конкретного екземпляра серверної платформи |
| strategy.json | Опис змінних, які використовуються в стратегіях `strategies` |
| transport.json | Опис змінних транспортів (наразі для `HTTP` та `Websocket`) |

## Конфігурація бізнес-логіки

| Назва змінної | Повний шлях доступу до неї | Тип данних | Опис | Приклад | 
| :-----------: | :------------------------: | :--------: | :--: | :------ | 
| baseUrl | 'business.baseUrl' | string | Префікс, який буде відділяти маршрути до API від інших | /api <br/> Повний шлях наприклад буде: http://localhost:3000/api/... | 
| schemaPath | business.schemaPath | string | Шлях до корня розташування бізнес логіки | home/local/ninjasushi/business-schema | 
| blPath | business.blPath | string | Шлях до вхідної точки в бізнес-логіки | home/local/ninjasushi/business-schema/app.ts | 
| applications | business.applications | Application[] | Перелік змінних оточення застосунків | [ { "name": "SystemAdministrator", "private": true, "token": "#sayhEbvy1Dbv&" } ] | 

## Конфігурація бази данних

| Назва | Повний шлях доступу | Тип данних | Опис | Приклад | 
| :-----------: | :-----------------: | :--------: | :--: | :------ | 
| protocol | database.protocol | string | Протокол маршруту до бази даних | http | 
| host | database.host | string | Хост, на якому працює база даних | localhost | 
| port | database.port | number | Порт, на якому працює база даних | 5432 | 
| user | database.user | string | Логін користувача бази даних | root | 
| password | database.password | string | Пароль користувача бази даних | root | 
| database | database.database | string | Назва бази даних | test_database | 
| schema | database.schema | string | Назва схеми в базі даних | public | 
| type | database.type | string | Тип базни даних ([Повний перелік в Features](https://typeorm.io/)) | postgresql | 
| certificatePath | database.certificatePath | string | абсолютний шлях до SSL сертифікату | /home/admin/cerificate.pem | 

## Конфігурація менеджерів

| Назва | Повний шлях доступу | Тип данних | Опис | За замовчуванням |  Приклад | 
| :-----------: | :-----------------: | :--------: | :--: | :--: | :------ | 
| enable | manager.core.enable | boolean | Флаг підключення `core` менеджера | false | true | 
| protocol | manager.core.protocol | string | Протокол, на якому працює `core` менеджер | http | https | 
| host | manager.core.host | string | Хост, на якому працює `core` менеджер | localhost | localhost | 
| port | manager.core.port | number | Порт, на якому працює `core` менеджер | 15015 | 15015 | 
| url | manager.core.url | string | Загальний префікс доступу до ендпоїнтів | /managers/core/cli | /managers/business/cli | 
| application | manager.core.application | string | Назва застосунку, який використовується для взаємодії з CLI | SystemAdministrator | SystemAdministrator | 

## Конфігурація провайдерів

Ряд провайдерів потребують налаштування конфігураційний змінних, наприклад секрет при генерації JWT токенів чи ін. Конфігурація відповідного провайдера знаходиться в рамках об'єкта конкретного провайдера. Нижче перелік змінних всіх провайдерів:


| Назва | Повний шлях доступу | Тип данних | Опис | За замовчуванням |  Приклад | 
| :-----------: | :-----------------: | :--------: | :--: | :--: | :------ | 
| scrambler.randomBytes | providers.scrambler.randomBytes | number | Кількість випадкових байтів, які будуть додані до методів хешування | 32 | 16 | 
| scrambler.secret | providers.scrambler.secret | string | Секрет який буде доданий при генерації JWT токенів | borgytiza | citizenimumari | 
| scrambler.accessExpiredAt | providers.scrambler.accessExpiredAt | number | Час валідності токена доступу (в хвилинах) | 10 | 2 | 
| scrambler.refreshExpiredAt | providers.scrambler.refreshExpiredAt | string | Час валідності токена оновлення (дні) | 10d | 30d | 
| scrambler.salt | providers.scrambler.salt | number | Кількість солі, яка буде додана до JWT токена | 10 | 15 | 

