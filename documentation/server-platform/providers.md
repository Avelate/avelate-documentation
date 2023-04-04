# Providers

Провайдери `Providers` призначені, в рамках конкретного провайдера, приховувати та описувати реалізацію бібліотеки чи групи бібліотек. При чому, ці бібліотеки можуть бути як NPM пакетом так і вбудованими. 

> [!ATTENTION]
> Кожний провайдер `provider` має тип ініціалізації - `transient`. Тобто при виклиці методу кокретного провайдера відповідний екземпляр провайдера створюється, викликається метод та видаляється з пам'яті. Таким чином при роботі з провайдерами, екземпляри провайдера не можуть бути мутабельними.

Загальна структура провайдерів:

```
.
├── server.ts
├── ...
├── providers
|   └── scrambler.provider.ts
|   └── image.provider.ts
|   └── i10n.provider.ts
|   └── {provider-name}.provider.ts
├── agents
|   └── business.agent.ts
├── ...
```

Провайдери `providers` використовуються як в серверному ядрі так і в серверній бізнес-логіці, але не всі методи відповідного провайдера можуть бути доступними в серверній бізнес-логіці, це зв'язано з тим, що відповідна бібліотека чи группа бібліотек може описувати як системний функціонал, такий наприклад як підключення або налаштування роботи самої бібліотеки так і прикладний функціонал - конвертувати, створити хеш чи ін.

<<SCHEMA>>

> [!NOTE]
> Доступ до відповідного провайдера з системної бізнес-логіки надається завдяки агента бізнес-логіки. Кожний об'єкт, який стосується конкретного провайдера `provider` та який описується в агенті бізнес-логіки називається аналогічно назві модуля, наприклад: модуль провайдера шифрування має назву - `scrambler.provider.ts`, об'єкт провайдера в агенті бізнес-логіки називається - `scrambler`. 

## Перелік провайдерів та їх призначення

| Назва провайдера | Назва модуля | Реалізовані бібліотеки | Призначення |
| :--------------: | :----------: | :--------------------: | :---------- |
| DatabaseProvider | database.provider.ts | [typeorm](https://www.npmjs.com/package/typeorm) | Описує методи створення запитів до баз даних |
| ErrorProvider | error.provider.ts | - | Описує методи для створення помилок та виключень |
| I10nProvider | i10n.provider.ts | [i18next](https://www.npmjs.com/package/i18next) | Описує методи по роботі з локалізаціями |
| ImageProvider | image.provider.ts | [sharp](https://www.npmjs.com/package/sharp) | Описує методи по обробці картинок |
| MailProvider | mail.provider.ts | [nodemailer](https://www.npmjs.com/package/nodemailer) | Описує методи для роботи з електронною поштою |
| MeasureProvider | measure.provider.ts | [perf_hooks](https://nodejs.org/api/perf_hooks.html) | Описує методи замірів внутрішньої продуктивності бізнес-логіки |
| MemoryStorageProvider | memory-storage.provider.ts | [redis](https://www.npmjs.com/package/ioredis) | Описує методи по роботі з `InMemory` сховищем |
| ScramblerProvider | scrambler.provider.ts | [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) <br/> [crypto](https://nodejs.org/api/crypto.html) <br/> [bcrypt](https://www.npmjs.com/package/bcrypt) <br/>  | Описує методи шифрування та кешування |


> ![ATTENTION]
> Провайдери можуть використовувати змінні оточення. Повний перелік змінних та провайдерів, які їх використовують дивись [Створення та опис конфігурацій сервера]


Приклад реалізації провайдера шифрування та хешування:

```typescript
@injectable()
class ScramblerProvider implements IScramblerProvider {
  private _JWT_SECRET: string | undefined;

  constructor(
    @inject(CoreSymbols.ConfigurationService) private _confService: IConfigurationService
  ) {}

  public get uuid(): string {
    return crypto.randomUUID();
  }

  public get accessExpiredAt(): Date {
    const expiredAt = this._confService.getMandatory<number>('provider:scrambler:accessExpiredAt');
    return new Date(Date.now() + expiredAt * 60 * 1000);
  }

  public get refreshExpiredAt(): Date {
    const expiredAt = this._confService.getMandatory<number>('provider:scrambler:refreshExpiredAt');
    return new Date(Date.now() + expiredAt * 60 * 1000);
  }

  public createHash(payload: string, algorithm: Algorithm = 'sha256'): string {
    const randomBytes = this._confService.getNumber('provider:scrambler:randomBytes', 32);
    const bytes = crypto.randomBytes(randomBytes).toString('hex');
    return crypto.createHash(algorithm).update(bytes).digest('hex');
  }

  public generateAccessToken(payload: string, algorithm?: Algorithm): string {
    const expiresIn = this._confService.getMandatory<number>('provider:scrambler:accessExpiredAt');
    return this.generateToken(payload, expiresIn, algorithm);
  }

  public generateRefreshToken(payload: string, algorithm?: Algorithm): string {
    const expiresIn = this._confService.getMandatory<number>('provider:scrambler:refreshExpiredAt');
    return this.generateToken(payload, expiresIn, algorithm);
  }

  public async verifyToken(token: string): Promise<JwtTokenPayload> {
    return new Promise((resolve, reject) => {
      if (!this._JWT_SECRET) {
        this._JWT_SECRET = this._confService.getMandatory<string>('provider:scrambler:secret');
      }
      jwt.verify(token, this._JWT_SECRET, (err, data) => {
        if (err) return reject(err);
        return resolve(data as JwtTokenPayload);
      });
    });
  }

  public async hashedPassword(password: string): Promise<string> {
    const salt = this._confService.getNumber('provider:scrambler:salt', 10);
    return bcrypt.hash(password, salt);
  }

  public async comparePassword(candidatePassword: string, userPassword: string): Promise<boolean> {
    return await bcrypt.compare(candidatePassword, userPassword);
  }

  private generateToken(payload: string, expiresIn: number, algorithm: Algorithm = 'sha256') {
    if (!this._JWT_SECRET) {
      this._JWT_SECRET = this._confService.getMandatory<string>('provider:scrambler:secret');
    }
    const token = this.createHash(payload, algorithm);

    return jwt.sign({ payload: payload + token }, this._JWT_SECRET, {
      expiresIn,
    });
  }
}

export default ScramblerProvider;
```

> [!TIP]
> З розвитком ядра, будуть розширятись можливості як існуючих провайдерів `providers` так і з'являтись нові. Для коректного розширення функціоналу існуючих провайдерів, кожний метод провайдера являє собою прості чисті функції, які зазвичай приймають малу кількість аргументів (1 - 2) та виконують лише одну дію - іншими словами є атомарними. 
> При потребі переписання існуючого методу на більш продуктивний чи з іншим причин, новий метод отримає назву, яка буде синонімом до існуючої, а в існуючий додасться вивід в консоль надпису про те, що в наступній мажорній версії існуючий метод буде видалений. Таким чином, кодова база не буде захламлятись старим функціоналом, а застосунки, які використовують `legacy` код - матимуть час на коригування того чи іншого методу.
> При потребі створення нового провайдера буде здійснено:
> - Доданий новий клас провайдера в директорії провайдерів `providers` з реалізацією відповідної бібліотеки / бібліотек.
> - Створений символ та поєднаний клас з символом та визначений `TransientScope` в контейнері інверсії залежностей `ioc`.
> - Методи, які потенційно можуть бути використані в бізнес-логіці просуваються в об'єкт (який одноіменний назві класу провайдера) перелік методів.
> - В модулях серверного ядра, де необхідно використати методи нового провайдера здійснюється `inject` нового провайдера.