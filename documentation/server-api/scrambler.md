# ScramblerProvider {docsify-ignore-all}

> [!TIP]
> **[Стабільність модуля]: 2 - Cтабільний**

Провайдер шифрування `ScramblerProvider` призначений для шифрування, дешифрування та створення хешів. Він може використовуватись в будь-якому місці бізнес-логіки де є потреба в шифрування та дешифруванні строкових данних, а також генерації хешів, наприклад для хешування паролів або генерації токенів доступу (`access token`, `refresh token`).

<hr/>

## Зміст
- [ScramblerProvider](#scramblerprovider)
    - methods:
        - [ScramblerProvider.generateAccessToken](#scramblerprovidergenerateaccesstoken)
        - [ScramblerProvider.generateRefreshToken](#scramblerprovidergeneraterefreshtoken)
        - [ScramblerProvider.createHash](#scramblerprovidercreatehash)
        - [ScramblerProvider.verifyToken](#scramblerproviderverifytoken)
        - [ScramblerProvider.hashedPassword](#scramblerproviderhashedpassword)
        - [ScramblerProvider.comparePassword](#scramblerprovidercomparepassword)
    - getters:
        - [ScramblerProvider.uuid](#scramblerprovideruuid)
        - [ScramblerProvider.accessExpiredAt](#scramblerprovideraccessexpiredat)
        - [ScramblerProvider.refreshExpiredAt](#scramblerproviderrefreshexpiredat)


## ScramblerProvider.generateAccessToken
<br/>

> [!TIP]
> [Стабільність]: 2 - Cтабільний

Генерує та надає токен доступу `access token`.
Час сплину може бути вказаний перемінною оточення в конфігураційному файлі за шляхом `PROVIDERS.SCRAMBLER.ACCESS_EXPIRED_AT` 
та повинен бути вказаний в **секундах**. За замовчуванням час сплину токена доступу складає **25** хвилин.

### Параметри

| Параметр | Порядок | Назва | Тип | Обов'язковість | Деталі |
| :---: |:---:|:---:|:---:|:---:| :--- |
| Вхідний| 1 | payload | string | Так | |
| Вхідний | 2 | algorithm | string | Ні | За замовчуванням `sha256` |
| Вихідний | 1 | - | string | Так

Повний переклад можливих алгоритмів шифрування:
- MD4
- MD5
- sha1
- sha256
- sha512
- SM3

### Приклад

```typescript
const token = bAgent.scrambler.generateAccessToken(userId)
```

>[!WARNING]
> Опис створення конфігураційних файлів та змінних оточення дивись [конфігураційні файли]()

[На вверх](#зміст) :arrow_up:

## ScramblerProvider.generateRefreshToken
<br/>

> [!TIP]
> [Стабільність]: 2 - Cтабільний

Генерує та надає токен оновлення `refresh token`.
Необхідно явно зазначити змінну оточення, яка відповідає за унікальний секретний ідентифікатор системи за шляхом `PROVIDERS.SCRAMBLER.SECRET` та повинен мати строковий тип.
<br/>

Окремо, час сплину може бути вказаний перемінною оточення в конфігураційному файлі за шляхом `PROVIDERS.SCRAMBLER.REFRESH_EXPIRED_AT`
та повинен бути вказаний в **секундах**. За замовчуванням час сплину токена доступу складає **30** днів.

### Параметри

| Параметр | Порядок | Назва | Тип | Обов'язковість | Деталі |
| :---: |:---:|:---:|:---:|:---:| :---: |
| Вхідний| 1 | payload | string | Так | |
| Вхідний | 2 | algorithm | string | Ні | За замовчуванням `sha256` |
| Вихідний | 1 | - | string | Так

Повний переклад можливих алгоритмів шифрування:
- MD4
- MD5
- sha1
- sha256
- sha512
- SM3

### Приклад

```typescript
const token = bAgent.scrambler.generateRefreshToken(userId)
```

>[!WARNING]
> Опис створення конфігураційних файлів та змінних оточення дивись [конфігураційні файли]()

[На вверх](#зміст) :arrow_up:

## ScramblerProvider.createHash

Cтворює та повертає хеш.
Необхідно явно зазначити змінну оточення, яка відповідає за унікальний секретний ідентифікатор системи за шляхом `PROVIDERS.SCRAMBLER.SECRET` та повинен мати строковий тип.
<br/>

Окремо, кількість випадкових байтів може бути задана перемінною оточення в конфігураційному файлі за шляхом `PROVIDERS.SCRAMBLER.RANDOM_BYTES` та повинен бути вказаний в штуках. За замовчуванням кількість випадкових байтів складає 32 символи.

### Параметри

| Параметр | Порядок | Назва | Тип | Обов'язковість | Деталі |
| :---: |:---:|:---:|:---:|:---:| :---: |
| Вхідний| 1 | payload | string | Так | |
| Вхідний | 2 | algorithm | string | Ні | За замовчуванням `sha256` |
| Вихідний | 1 | - | string | Так

Повний переклад можливих алгоритмів шифрування:
- MD4
- MD5
- sha1
- sha256
- sha512
- SM3

### Приклад

```typescript
const login = 'test-user';
const token = bAgent.scrambler.generateRefreshToken(login);
```

>[!WARNING]
> Опис створення конфігураційних файлів та змінних оточення дивись [конфігураційні файли]()

[На вверх](#зміст) :arrow_up:

## ScramblerProvider.verifyToken

Перевіряє валідність токену та надає дані про цей токен. Необхідно явно зазначити змінну оточення, яка відповідає за унікальний секретний ідентифікатор системи за шляхом `PROVIDERS.SCRAMBLER.SECRET` та повинен мати строковий тип.

### Параметри

| Параметр | Порядок | Назва | Тип | Обов'язковість | Деталі |
| :---: |:---:|:---:|:---:|:---:| :--- |
| Вхідний| 1 | token | string | Так | |
| Вихідний | 1 | payload | string | Так | Публічні дані
| Вихідний | 2 | iat | number | Так | Час створення токена
| Вихідний | 3 | exp | number | Так | Час сплину

### Приклад



## ScramblerProvider.hashedPassword




## ScramblerProvider.comparePassword



## ScramblerProvider.uuid

## ScramblerProvider.accessExpiredAt

## ScramblerProvider.refreshExpiredAt








