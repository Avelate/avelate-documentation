# Swagger

Документ документатор `swagger` призначений для створення API документації конкретної колекції.

> [!NOTE]
> Серверне ядро інкапсулює в собі реалізацію формат опису документації в Swagger API стилі та безпосередньо використовує саме бібліотеку `Swagger` або `OpenAPI` для генерації кінцевої документації. Опис деталей створення документації завдяки `Swagger` дивись [Swagger документація](https://swagger.io/)

Сумарно, з групи таких документів завантажувач API документації `swagger loader` автоматично згенерує повний список API документації, до якої є можливість отримати доступ за шляхом:

```
{{procotol}}://{{host}}:{{port}}/api-documentation/{{application-name}}
```

Наприклад шлях до застосунку "Системний адміністратор":

```
http://localhost:3000/api-documentation/SystemAdministrator
```

> [!ATTENTION]
> Доступ до API документації конкретного застосунка може бути як приватним так і публічним, завдяки чому власник серверного застосунку за потреби може:
> - надати одній компанії доступ до одни API, а других компанії надати доступ до других API. 
> - зробити публічним API відповідного застосунку або групи затсосунків.

![AdapterSwagger](./documents-png/AbstractSwagger.png)

> [!ATTENTION]
> Кожний документатор `swagger` повинен успадковуватись від абстрактного документатора `abstract swagger`, в іншому випадку опис API документації не буде включений в загальну схему API документації.

Опис документації конкретної колекції складається з:
- `tag` - загальний спільний тег який є назваою колекції. Деталі дивись [Значення тегів Swagger документації](https://swagger.io/docs/specification/grouping-operations-with-tags/)
- `routines` - колекції маршрутів, необхідно створювати всередині методу `addRoutines`.
- `path` - шлях доступу конкретного `endpoint`. Рекомендується опис маршрутів виносити в окремий файл `{{collection.name}}.router.path.ts` оскільки шляхи використовуються в декількох місцях колекції. Деталі дивись [Опис маршрутів Swagger колекції](https://swagger.io/docs/specification/paths-and-operations/)
- `method` - HTTP метод, який закріплений за маршрутом. Наприклад `get`, `post`, `put` і т.д.
- `summary` - короткий опис того, що здійснює конкретний маршрут.
- `body` - схема даних тіла запиту. Рекомендується створювати схему валідації вхідних даних запиту. Ця схема в подальшому імпортується в відповідний маршрут при описі Swagger документації.
- `responses` - `Set` колекція відповідей конкретного маршруту. Може включати в собі декілька типів відповідей, наприклад при відсутності авторизації, успішного чи помилкового виконання. Деталі дивись [Опис відповідей маршрутів в Swagger документації](https://swagger.io/docs/specification/describing-responses/)
    - `code` - HTPP status код. Повинний бути унікальний для конкретної відповіді конкретного маршруту.
    - `description` - опис характеру відповіді конкретного маршруту.
    - `content` - вміст відповіді, який складається з?
        - `mediaType` - тип вмісту відповіді. За замовчуванням `application/json`.
        - `schema` - схема даних тіла відповіді. Бажано створювати схеми даних в валідаторі `validator`.


Приклад створення опису документації конкретної колекції:


```typescript
import AuthRouterPath from './auth.router.path';
import { SwaggerResponse } from '@CoreTypes/abstract-documents/abstracts.swagger';
import { inject, injectable } from 'inversify';
import ninjaSushiSymbols from '../../../../ioc/ioc.ninjasushi.symbols';
import { IAuthValidator } from '@CollectionsTypes/ninjasushi/collections/auth/auth.validator';
import { AbstractSwagger, HttpCode, swagger } from '@Vendor';
import CollectionNames from '../../../../common/collection.name';

@injectable()
class AuthSwagger extends AbstractSwagger {
  protected tag = CollectionNames.AUTH;
  protected routines: Set<SwaggerRoutine>

  constructor(@inject(ninjaSushiSymbols.AuthValidator) private authValidator: IAuthValidator) {
    super();
  }

  protected addRoutines() {
    this.routines = new Set<SwaggerRoutine>()
    this.routines.add({
      path: AuthRouterPath.SIGNUP,
      method: 'post',
      summary: 'Sign up new user',
      responses: new Set<SwaggerResponse>([
        {
          code: HttpCode.SUCCESS,
          description: 'Successfully sign up new user',
          content: {
            mediaType: 'application/json',
            schema: this.authValidator.SignupSchema,
          },
        },
      ]),
    });

    this.routines.add({
      path: AuthRouterPath.LOGOUT,
      method: 'get',
      summary: 'Logout user from account',
      security: 'bearer',
      responses: new Set<SwaggerResponse>([
        {
          code: HttpCode.SUCCESS,
          description: 'User successfully logout',
        },
      ]),
    });
  }
}

export default AuthSwagger;
```

Деталі реалізації абстрактного документ
атора `abstract swagger` дивись [AbstractSwagger](../server-platform/abstract-documents.md#swagger)