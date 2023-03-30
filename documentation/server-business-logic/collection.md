# Collection

Колекції це основний компонент при створені бізнес-логіки. Колекціії описують окремі домені області в моделі `DDD (Domain Driven Design)`. Кожна колекція складається з завчасно зарезервованих документів, які описують кожну зону доменної області. 

> [!NOTE]
> Домена область відповідає за створення, опис та обробку однієї сутності бізнес-логіки. Такою областю можуть бути, наприклад: `users` - користувачі чи `product` - товари. 
> Зазвичай домена область виділяє в собі окремий елемент бізнес процесу, який може також виступати актором.


Cтруктура колекції на прикладі колекції авторизації `auth`:

```
└── collections
|    └── auth
|    |    └── auth.collector.ts
|    |    └── auth.controller.ts
|    |    └── auth.middleware.ts
|    |    └── auth.router.path.ts
|    |    └── auth.router.ts
|    |    └── auth.swagger.ts
|    |    └── auth.asyncapi.ts
|    |    └── auth.validator.ts
|    |    └── auth.i10n.en.ts
|    |    └── auth.i10n.ru.ts
|    |    └── auth.i10n.ru.ts
|    └── [collection-name]
|    |    └── [collection-name].collector.ts
|    |    └── [collection-name].controller.ts
|    |    └── [collection-name].repository.ts
|    |    └── auth.router.path.ts
|    |    └── ...
```

Перелік та призначення документів:

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