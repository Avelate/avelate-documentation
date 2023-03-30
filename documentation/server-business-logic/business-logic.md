
Бізнес-логіка складається з переліку застосунків, які під'єднуються до модуля списку застосунків `ApplicationList`. Кожний застосунок обов'язково складається з колекцій `collection`, які в свою чергу складаються з документів `document`, та може складатись з необов'язкових доповнень, наприклад боту `Telegram bot` та ін. 

## Структура бізнес-логіки

Детальний приклад структури:

```
.
├── app.ts
├── apps
|   └── system-admin
|   |    └── collections
|   |    |    └── [collection-name]
|   |    └── system-admin.application.ts
|   └── business-admin
|   |    └── collections
|   |    |    └── [collection-name]
|   |    └── business-admin.application.ts
|   └── ninjasushi
|   |    └── collections
|   |    |    └── auth
|   |    |    |    └── auth.collector.ts
|   |    |    |    └── auth.controller.ts
|   |    |    |    └── auth.middleware.ts
|   |    |    |    └── auth.router.path.ts
|   |    |    |    └── auth.router.ts
|   |    |    |    └── auth.swagger.ts
|   |    |    |    └── auth.asyncapi.ts
|   |    |    |    └── auth.validator.ts
|   |    |    |    └── auth.i10n.en.ts
|   |    |    |    └── auth.i10n.ru.ts
|   |    |    |    └── auth.i10n.[language-name].ts
|   |    |    └── user
|   |    |    |    └── user.collector.ts
|   |    |    |    └── user.controller.ts
|   |    |    |    └── user.entity.ts
|   |    |    |    └── user.repository.ts
|   |    |    |    └── user.[document-type].ts
|   |    |    └── [collection-name]
|   |    |         └── [collection-name].[document-type].ts
|   |    └── ninjasushi.application.ts
|   └── [application-name]
├── common
|    └── application-names.ts
|    └── collection-names.system-admin.ts
|    └── collection-names.business-admin.ts
|    └── collection-names.ninjasushi.ts
|    └── collection-names.[application-name].ts
|    └── entity-names.system-admin.ts //  Перелік назв схем таблиць в базі даних застосунку "Системний адміністратор".
|    └── entity-names.business-admin.ts
|    └── entity-names.ninjasushi.ts
|    └── entity-names.[application-name].ts
├── ioc
|    └── ioc.system-admin.symbols.ts
|    └── ioc.system-admin.ts
|    └── ioc.business-admin.symbols.ts
|    └── ioc.business-admin.ts
|    └── ioc.ninjasushi.symbols.ts
|    └── ioc.ninjasushi.ts
|    └── ioc.[application-name].symbols.ts
|    └── ioc.[application-name].ts
|    └── ioc.ts
```

Де:
- `app.ts` - вхідна точка входу, в якій повинен експортуватись екзепляр списку застосунків `ApplicationList`, та до якого необхідно вказати абсолютний шлях в конфігураційному файлі.
- `apps` - директорія всіх застосунків.
    - `system-admin` - директорія застосунку "Системний адміністратор".
    - `business-admin` - директорія застосунку "Бізнес адміністратор".
    - `ninjasushi` - директорія застосунку "Ninja Суші".
        - `collections` - директорія колекцій будь-якого застосунку.
            - `collection-name` - будь-яка назва конкретної колекції.
            - `auth` - колекція авторизації `auth`.
                - `auth.collector.ts` - реєстрація всіх документів колекції.
                - `auth.controller.ts` - опис основних обробників.
                - `auth.middleware.ts` - опис проміжних обробників.
                - `auth.router.path.ts` - перелік маршрутів.
                - `auth.router.ts` - реєстрація обробників по відповідним маршрутам.
                - `auth.swagger.ts` - описання `Swagger` документації.
                - `auth.asyncapi.ts` - описання `AsyncApi` документації.
                - `auth.validator.ts` - документ, який писує валідації `body` відповідного маршруту колекції.
                - `auth.i10n.ru.ts` - словник мови фраз колекції російською мовою.
                - `auth.i10n.en.ts` - словник мови фраз колекції англійською мовою.
                - `auth.i10n.[language-name].ts` - словник мови фраз колекції на відповідній мові.
            - `user` - колекція користувачів `user`.
                - `user.collector.ts` - реєстрація всіх документів колекції.
                - `user.controller.ts` - опис основних обробників.
                - `user.entity.ts` - опис схеми таблиці в базі даних.
                - `user.repository.ts` - перелік обробників репозиторію `repository`.
                - `user.[document-type].ts` - інший будь-який документ `user` колекції.
        - `ninjasushi.application.ts` - реєстрація всіх колекцій застосунку "Ninja Суші".
        - `[application-name].application.ts` -  реєстрація всіх колекцій будь-якого застосунку.
    - `[application-name]` - директорія будь-якого застосунку.
- `common` - загальні модулі, які впроваджуються в будь-яку колекцію застосунку / застосунків.
     - `application-names.ts` - перелік назв застосунків.
     - `collection-names.system-admin.ts` - перелік назв колекцій застосунку "Системний адміністратор".
     - `collection-names.business-admin.ts` - перелік назв колекцій застосунку "Бізнес адміністратор".
     - `collection-names.ninjasushi.ts` - перелік назв колекцій застосунку "Ninja Суші".
     - `collection-names.[application-name].ts` - перелік назва колекцій будь-якого застосунку.
     - `entity-names.system-admin.ts` - перелік назв схем таблиць в базі даних застосунку "Системний адміністратор".
     - `entity-names.business-admin.ts` - перелік назв схем таблиць в базі даних застосунку "Бізнес адміністратор".
     - `entity-names.ninjasushi.ts` - перелік назв схем таблиць в базі даних застосунку "Ninja Суші".
     - `entity-names.[application-name].ts` - перелік назв схем таблиць в базі даних будь-якого застосунку.
- `ioc` - директорія з контейнера інверсії залежностей застосунків.
    - `ioc.system-admin.symbols.ts` - Перелік назв документів колекцій застосунку "Системний адміністратор".
    - `ioc.system-admin.ts` - Контейнер інверсії залежностей документів колекцій застосунку "Системний адміністратор".
    - `ioc.business-admin.symbols.ts` - Перелік назв документів колекцій застосунку "Бізнес адміністратор".
    - `ioc.business-admin.ts` - Контейнер інверсії залежностей документів колекцій застосунку "Бізнес адміністратор".
    - `ioc.ninjasushi.symbols.ts` - Перелік назв документів колекцій застосунку "Ninja Суші".
    - `ioc.ninjasushi.ts` - Контейнер інверсії залежностей документів колекцій застосунку "Ninja Суші".
    - `ioc.[application-name].symbols.ts` - Перелік назв документів колекцій будь-якого застосунку.
    - `ioc.[application-name].ts` - Контейнер інверсії залежностей документів колекцій будь-якого застосунку.
    - `ioc.ts` - Результуючий контейнер інверсії залежностей, який об'єднує всі контейнери застосунків.


## Конфігурація бізнес-логіки