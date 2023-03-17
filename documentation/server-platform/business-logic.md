# Бізнес-логіка

Бізнес-логіка складається з переліку застосунків, які під'єднуються до модуля списку застосунків `ApplicationList`. Кожний застосунок обов'язково складається з колекцій `collection`, які в свою чергу складаються з документів `document`, та може складатись з необов'язкових доповнень, наприклад боту `Telegram bot` та ін. (повний перелік складових застосунку дивись [Застосунок](#застосунок))

## Структура бізнес-логіки

.
├── app.ts // Вхідна точка входу, в якій повинен експортуватись екзепляр списку застосунків `ApplicationList`.
├── apps // Директорія всіх застосунків.
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

Яка складається з:
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


## Список застосунків

Вхідною точкою бізнес-логіки є список застосунків `ApplicationList`. Кожний список застосунків повинен бути унаслідований від абстрактного списку застосунків `AbstractApplicationList` та, для більшої зручності, можеж реалізувовувати інтефейс `IApplicationList`.

> [!WARNING]
> Список застосунків повинен бути обов'язково наслідуватись від абстрактного списку застосунків оскільки, декларативний запис застосунків в загальну схему бізнес-логіки відбувається завдяки абстрактному списку застосунків `AbstractApplicationList`. В відсутності наслідування список застосунків не буде включений в схему бізнес-логіки, а отже сервер не матиме в пам'яті кодової бази застосунків.


При створені будь-якого застосунку, він повинен бути зарєстрований в списку застосунків `application-list.ts`. Для прикладу підключення 3х застосунків: системний адміністратор `SystemAdmin`, адміністраторів групи ресторанів Ніндзя `BusinessNinjaAdmin`, Ресторан суш Ніндзя Суші `NinjaSushi`:

```typescript
@injectable()
class ApplicationList extends AbstractApplicationList implements IAbstractApplicationList {
  protected applications = new Set<IApplication>();

  constructor(
    @inject(SystemAdminSymbols.Application) private systemNinja: IAbstractApplication,
    @inject(BusinessNinjaAdminSymbols.Application) private businessNinja: IAbstractApplication,
    @inject(NinjaSushiSymbols.Application) private ninjaSushi: IAbstractApplication,
  ) {
        super();
    }

  protected setApplications() {
    this.applications.add(this.systemNinja);
    this.applications.add(this.businessNinja);
    this.applications.add(this.ninjaSushi);
  }
}
```

Таким чином бізнес схема буде налічувати 3 різних серверних застосунка.


> [!ATTENTION]
> Необхідно вказати абсолютний шлях до списку застосунків `ApplicationList` в конфігураційному файлі, для того, щоб підключити бізнес-логіку до серверного ядра.

## Застосунок

API кожного застосунку складається з колекцій `collection`, яка в свою чергу складається з документів `document`. Кожний застосунок повинен бути наслідуватись від абстрактного застосунку.

> [!WARNING]
> Кожний застосунок повинен бути обов'язково наслідуватись від абстрактного застосунку `AbstractApplication` оскільки, декларативний запис застосунків в загальну схему бізнес-логіки відбувається завдяки абстрактному застосунку `AbstractApplication`. В відсутності наслідування застосунок не буде включений в схему бізнес-логіки, а отже сервер не матиме в пам'яті кодової бази застосунку.


Наслідування від абстрактного застосунку `AbstractApplication`, створює декларативну потребу в заповнені наступних данних:

- `name` - Тип `string` - Назва застосунку.
- `info` - Тип `object` - Короткий опис інформації по застосунку, необхідний для створення `Swagger` та `AsyncApi` документацій, та складається з:
    - `title` - Тип `string` - Заголовок документації.
    - `description` - Тип `string` - Короткий опис документації.
    - `contact` - Тип `string` - Контактна особа, яка створила документацію.
    - `version` - Тип `string` - Версія документації.
- `collections` - `Set<Collection>` - Ініціалізація колекції `Set`, яка складається з колекцій `Collection`.

 Кожна колекція повинна бути зареєстрована в вхідній точці `application.[name].ts`. Для прикладу підключення двох колекцій: авторизації `auth` та користувачі `users`:

```typescript
@injectable()
class NinjaSushiApplication extends AbstractApplication implements IAbstractApplication {
  protected name = ApplicationNames.NINJA_SUSHI;
  protected info = {
    title: 'Ninja Sushi Api',
    description: 'Application for sushi e-commerce shop',
    contact: 'borys.britva@gmail.com',
    version: '1.0.0',
  };
  protected collections = new Set<IAbstractCollection>();

  constructor(
    @inject(NinjaSushiSymbols.AuthCollector) private auth: IAbstractCollector,
    @inject(NinjaSushiSymbols.UserCollector) private user: IAbstractCollector
  ) {
    super();
  }

  protected setCollections(): void {
    this.collections.add(this.auth);
    this.collections.add(this.user);
  }
}
```

> [!NOTE]
> Бізнес-логіка підтримує реєстрацію однієї і ті є ж колекції в різних застосунків, що суттєво зменшує кількість написання кодової бази.



### Перелік документів 

| Назва | Позначення | Опис | 
| :---: | :--------: | :--- | 
| [Колекціонер](#контролер) | `[collection-name].collector.ts` | Реєстрація всіх документів колекції | 
| Контролер | `[collection-name].controller.ts` | Опис основних обробників маршрутів | 
| Проміжний обробник | `[collection-name].middleware.ts` | Опис проміжних обробників маршрутів | 
| Маршрути | `[collection-name].router.path.ts` | Перелік машрутів | 
| Маршрутизатор | `[collection-name].router.ts` | Реєстрація маршрутів та їх обробників | 
| `Swagger` документатор | `[collection-name].swagger.ts` | Опис `http` запитів в `Swagger` документації | 
| `AsyncApi` документатор | `[collection-name].asyncapi.ts` | Опис `websocket` подій в `AsyncApi` документації | 
| Валідатор | `[collection-name].validator.ts` | Опис схем валідацій обробників | 
| Словник мови | `[collection-name].i10n.[language.name].ts` | Опис фраз мови колеції | 
| Схема сутності | `[collection-name].entity.ts` | Схема сутності таблиці в базі даних | 
| Репозиторій | `[collection-name].repository.ts` | Репозиторій колекції | 
| Помічник | `[collection-name].helper.ts` | Опис допоміжних обробників | 
| Виконувач | `[collection-name].executor.ts` | Опис запланованих обробників | 


## Контролер

Контролер призначени для опису основних обробників маршрутів. 

<hr/>

Контролер кожної колекції повинен наслідуватись від абстрактного контролера `AbstractController`, таким чином контролер колекції `[collection-name].controller.ts` отримає доступ до агентів.

> [!ATTENTION]
> Агенти надаються абстрактним контролером `AbstractController`, тобто контролер колекції може отримати екземлпяр відповідного агента через `this`, наприклад доступ до агента бізнес-логіки `BusinessLogicAgent` виглядає так: `this.bAgent.[scope].[method]`, де: `scope` - назва об'єкту відповідної області призначення, наприклад `session`, а `method` - конкретний ментод цього об'єкту області призначення.

Контролер колекції повинен складатись з переліку обробників. Кожний обробник має однаковий контракт функції:

| Параметр | Порядок | Тип  | Обов'язковість | Опис |
| :------: | :-----: | :--: | :------------: | :--- |
| Вхідний | 1 | [HandlerRequest](#handlerrequest) | Так | `http` об'єкт запиту. | 
| Вхідний | 2 | [HandlerResponse](#handlerresponse) | Так | `http` об'єкт відповіді. | 
| Вхідний | 3 | [Options](#options) | Ні | Опції обробника | 
| Вихідний | 1 | Promise<[HandlerResult](#handlerresult)> | Так | Результат роботи обробника | 


### HandlerRequest

Об'єкт `http` запиту, який складається:

| Назва | Тип  | Опис | 
| :---: | :--: | :--- | 
| `body` | Record<string, unknown> | Тіло запиту | 
| `headers` | Record<string, string> | Заголовки запиту | 
| `params` | Record<string, string> | Параметри запиту | 
| `query` | string | Стрічка запиту | 
| `originalUrl` | string | Адреса маршруту | 

### HandlerResponse

Екземпляр класу `http` відповіді, який описаний за паттерном `Builder` (методи класу повертаються контекст цього класу).

| Назва | Тип  | Опис | 
| :---: | :--: | :--- | 
| `body` | Record<string, unknown> | Тіло запиту | 

### Options

Опції обробника



### HandlerResult


> [!ATTENTION]
> Кожний обробник повинен бути стрілочною функцією.

### Інтеграція документу

Кожний контролер повинен бути, в послідуючому, доданий в залежність до документа маршрутизатора `[collection-name].router.ts`. Та при описані конкретного маршшруту необхідно інтегрувати основний обробник до відповідної адреси маршруту.

> [!ATTENTION]
> Рекомендується називати обробники за назвою адреси маршруту для зрозумілого зв'язування.
> Наприклад:
> - Назва колекції:  `auth`.
> - Назва обробника: `signup`. 
> - Адреса маршруту: `auth/signup`.
> - Назва документа колекції: 
>   - Контролер: `auth.controller.ts`.
>   - Маршрути: `auth.router.path.ts`.
>   - Маршрутизатор: `auth.router.ts`.


### Приклад



## Проміжний обробник

## Маршрути

## Маршрутизатор

## Swagger документатор

## AsyncApi документатор

## Валідатор

## Схема сутності

## Репозиторій

## Помічник

## Робітник