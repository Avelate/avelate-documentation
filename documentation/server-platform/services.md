# Сервіси

Кожний сервіс визначається на основі потенційного суттєвого навантаження на конкретний функціонал.
Так, організація роботи з бібліотекою перекладів – це виклик одного й того ж метода,
який повертає той чи інший переклад в залежності від типу мови.
Організація ж логування при високих потребах в створені логів з великою деталізацією може зайняти весь
головний процес сервера, що унеможливлює його використання при потребі обробки великої кількості запитів різним джерелам взаємодії.
Через це сервіс логування має можливість горизонтального масштабування  - винесення логера в окремий веб-сервер, який займається виключно написанням файлів з логами.
Іншою стороною медалі є постійна необхідність присутності екземпляру сервісу в оперативній пам’яті для постійного
звернення до нього, що наприклад може бути здійснене з сервісом конфігурацій, через це тип створення екземплярів для сервісів відповідає Singleton scope.
Через те, що сервіси повинні бути запущені в конкретному порядку, кожний сервіс наслідує
від абстрактного сервісу два методи, один з яких відповідає за запуск, а інший за зупинку того чи іншого сервісу




Кожен сервіс, наслідуючись від абстрактного сервісу(`AbstractService`) повинен реалізувати хуки `onStart` та `onStop`, які
виконані при запуску чи відповідно зупинці сервісу. <br/>
Простий приклад сервісу конфігураціЇ:

```typescript
@injectable()
class ConfigurationService extends AbstractService implements IConfigurationService {
    protected serviceName = 'ConfigurationService'
    protected logger: undefined

    constructor() {
        super()
    }

    protected onStart(): Promise<boolean> {
        return Promise.resolve(true);
    }

    protected async onStop(): Promise<void> {
        // not implemented
    }

    protected get<T extends string | number | boolean>(name: string, def?: string | number | boolean): string | number | boolean | undefined {
        try {
            const variable = config.get<T>(name)
            if (variable === '') {
                return def
            }
            return variable
        } catch (_) {
            return def
        }
    }

    ...
}

export default ConfigurationService
```

Сервіси просуваються як залежності до з'єднувача серверу `ServerConnection`, де влаштовуються в необхідному порядку їх ініціалазації:

```typescript

@injectable()
class ServerConnection extends AbstractConnection implements IServerConnection {
    private _httpServer: http.Server | https.Server | undefined

    constructor(
        @inject(platformSymbols.ConfigurationService) private confService: IConfigurationService,
        @inject(platformSymbols.LoggerService) protected loggerService: ILoggerService,
        @inject(platformSymbols.DatabaseService) private dbService: IDatabaseService,
    ) {
        super();
    }

    private async startServices() {
        await this.confService.start()
        await this.loggerService.start()
        await this.dbService.start()
    }

    private async stopServices() {
        await this.dbService.stop()
        await this.loggerService.stop()
        await this.confService.stop()
    }

    ...
}

export default ServerConnection
```

Таким чином загальна схема створення та підключення сервісів виглядає так:


```mermaid
flowchart LR
    PlatformIoc1(((platform.ioc.ts))) ---->|"@inject<br/><br/>Services"|ServerConnection[[ServerConnection.ts]]
    ConfigurationService(ConfigurationService.ts) --->|bind<br/><br/>SingletonScope| PlatformIoc1(((platform.ioc.ts)))
    LoggerService(LoggerService.ts) --->|bind<br/><br/>SingletonScope| PlatformIoc1(((platform.ioc.ts)))
    ServiceDots(  ...  ) --->|bind<br/><br/>SingletonScope| PlatformIoc1(((platform.ioc.ts)))
    AbstractService(AbstractService) ---> ConfigurationService(ConfigurationService.ts)
    AbstractService(AbstractService) ---> LoggerService(LoggerService.ts)
    AbstractService(AbstractService) --->  ServiceDots(  ...  )
    As["Абстрактні методи<br/>-------------------------<br/>async start() {}<br/> async stop() {}"] -.- AbstractService(AbstractService)
```