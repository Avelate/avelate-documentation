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