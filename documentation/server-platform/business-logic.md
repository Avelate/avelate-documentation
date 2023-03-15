# Бізнес-логіка

Бізнес-логіка складається з переліку застосунків, які під'єднуються до модуля списку застосунків `ApplicationList`. Кожний застосунок обов'язково складається з колекцій `collection`, які в свою чергу складаються з документів `document`, та може складатись з необов'язкових доповнень, наприклад боту `Telegram bot` та ін. (повний перелік складових застосунку дивись [Застосунок](#застосунок))

## Структура бізнес-логіки

.
├── app
|   └── system-admin
|        └── collections
|        └── system-admin.applications.ts
|   └── business-admin
|   └── ninjasushi
|   └── [application-name]
├── common
|    └── application-names.ts
|    └── collection-names.ts
|    └── entity-names.ts
├── ioc
     └── ioc.system-admin.symbols.ts
     └── ioc.system-admin.ts
     └── ioc.business-admin.symbols.ts
     └── ioc.business-admin.ts
     └── ioc.ninjasushi.symbols.ts
     └── ioc.ninjasushi.ts
     └── ioc.[application-name].symbols.ts
     └── ioc.[application-name].ts
     └── ioc.ts


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

