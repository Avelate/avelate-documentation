## Загальний опис {docsify-ignore}

Платформа являє собою живий організм, який змінюється та розвивається,
а реалізовані застосунки, які працюють в робочому середовищі та потребують
підтримки напроти – потребують стабільності, у своєму розвитку. <br/><br/>
З часом кількість застосунків які працюють в робочому середовищі постійно збільшується, це означає, що зміни,
які рушать минулі контракти між ядром та сервером – можуть порушити роботу не одного десятка застосунків,
які потребують підтримки, та кожен, у свій час повинен бути перейти на нову версії серверної платформи.
З метою недопускання таких порушень створюється проміжний інтерфейс між ядром та бізнес-логікою. <br/> <br/>

Кожен застосунок має лише дві залежності:
-	відповідні агенти – проміжні інтерфейси, які надають функціонал ядра.
-	типи, які описують контракти – надаються через один файл з типізацією.

<br/>

```mermaid
    flowchart LR
    Core(((Core))) -.-> BusinessLogicAgent(BusinessLogicAgent.ts)
    Core(((Core))) -.-> IntegrationAgent(IntegrationAgent.ts)
    Core(((Core))) -.-> BaseOperationAgent(BaseOperationAgent.ts)
    Core(((Core))) -.-> InheritanceAgent(InheritanceAgent.ts)
    BusinessLogicAgent(BusinessLogicAgent.ts) --> IocPlatform[Ioc]
    IntegrationAgent(IntegrationAgent.ts) --> IocPlatform[Ioc]
    BaseOperationAgent(BaseOperationAgent.ts) --> IocPlatform[Ioc]
    InheritanceAgent(InheritanceAgent.ts) --> IocPlatform[Ioc]
    IocPlatform(((platform.ioc.ts))) --> Vendor(Vendor)
    Vendor[Vendor] --> as[(Business Logic)]
```

Кожен агент надає абстрактний функціонал згідно з назвою цього функціонала та призначення:
- Агент бізнес-логіки `BusinessLogicAgent` - надає функціонал сервісів та провайдерів.
- Агент інтеграцій `IntegrationAgent` - надає функціонал взаємодії з зовнішніми системами, підсистемами чи сервісами.
- Агент базових операцій `BaseOperationAgent` - надає функціонал базових операцій.
- Агент наслідування `InheritanceAgent` - надає класи (не екземпляри) для можливості наслідування від них документів колекцій.

> [!NOTE]
> Кожен агент налічує гетери, які описують собою належність тому чи іншому сервісу, інтеграції та ін. Таким чином при використанні агентів в документах бізнес-логіки
є чітке розуміння до якої абстракції відноситься той чи інший функціонал.

## Агент бізнес-логіки

Агент бізнес-логіки `BusinessLogicAgent` надає функціонал сервісів та провайдерів. 
Агент має додаткові абстракцію в вигляді геттеру, який має назву відповідного сервісу чи 
провайдера, що спрощує розуміння приналежності конкретного методу до конкретної сутності ядра.

```typescript
@injectable()
class BusinessLogicAgent implements IBusinessLogicAgent {
    constructor(
        @inject(CoreSymbols.ErrorProvider) private errorProvider: IErrorProvider,
        @inject(CoreSymbols.LoggerService) private loggerService: ILoggerService,

        @inject(CoreSymbols.DatabaseProvider) private dbProvider: IDatabaseProvider,
        @inject(CoreSymbols.MailerProvider) private mailerProvider: IMailerProvider,
        
        // ...
    ) {}

    public get tokenizerProvider() {
        return {
            getToken: (userId: string, type?: 'access' | 'refresh') => this.tokenizerProvider.getToken(userId, type),
            verifyToken: (token: string) => this.tokenizerProvider.verifyToken(token),
            getExpiredAt: () => this.tokenizerProvider.getExpiredAt()
        }
    }

    public get dbProvider() {
        return {
            getRepo: <T extends Record<string, unknown>>(entityName: string) => this.dbProvider.getRepository<T>(entityName)
        }
    }

    public get loggerService() {
        return {
            info: (message: string) => this.loggerService.info(message),
            error: (error: any) => this.loggerService.error(error)
        }
    }
    
    // ...
}

export default BusinessLogicAgent
```

Агент надає системний функціонал, що в 90% випадків використовується для створення схем валідації,
проміжних та основних обробників, документації API конкретної колекції, 
а також при потребі додавання готових проміжних чи основних обробників при створенні маршрутизації, 
наприклад обробник базової реєстрації через логін або пароль.


**Повна схема просування функціонала сервісів та провайдерів до відповідного документу будь-якої колекції:**

```mermaid
    flowchart LR
    
    IocPlatform(((platform.ioc.ts))) ==> Services((Services))
    subgraph Services
    ConfigurationService(ConfigurationService.ts)
    LoggerService(LoggerService.ts)
    AuthService(AuthService.ts)
    end
    
    IocPlatform(((platform.ioc.ts))) ==> Providers((Providers))
    subgraph Providers
    DatabaseProvider(DatabaseProvider.ts)
    TokenizerProvider(TokenizerProvider.ts)
    MailerProvider(MailerProvider.ts)
    end
    
    LoggerService(LoggerService.ts) --> LoggerMethods(Logger methods)
    ConfigurationService(ConfigurationService.ts) --> ConfigurationMethods[Configuration methods]
    AuthService(AuthService.ts) --> AuthMethods[Auth methods]
    
    LoggerMethods(Logger methods) --> BaAgent(((BusinessLogicAgent)))
    ConfigurationMethods[Configuration methods] --> BaAgent(((BusinessLogicAgent)))
    AuthMethods[Auth methods] --> BaAgent(((BusinessLogicAgent)))
    
    DatabaseProvider(DatabaseProvider.ts) --> DatabaseMethods(Database methods)
    TokenizerProvider(TokenizerProvider.ts) --> TokenizerMethods[Tokenizer methods]
    MailerProvider(MailerProvider.ts) --> MailerMethods[Mailer methods]
    
    DatabaseMethods(Database methods) --> BaAgent(((BusinessLogicAgent)))
    TokenizerMethods[Tokenizer methods] --> BaAgent(((BusinessLogicAgent)))
    MailerMethods[Mailer methods] --> BaAgent(((BusinessLogicAgent)))
    
    BaAgent(((BusinessLogicAgent))) ==> as[(CoreVendor.ts)]
    as[(CoreVendor.ts)] -.-> collController["[collection-name].controller.ts"]
    as[(CoreVendor.ts)] -.-> collValidator["[collection-name].validator.ts"]
    as[(CoreVendor.ts)] -.-> collMiddleware["[collection-name].middleware.ts"]
    as[(CoreVendor.ts)] -.-> collDocument["[collection-name].documentor.ts"]
    
    subgraph "[Collection]"
     collController["[collection-name].controller.ts"]
     collValidator["[collection-name].validator.ts"]
     collMiddleware["[collection-name].middleware.ts"]
     collDocument["[collection-name].documentor.ts"]
    end
```

<br/>

> [!NOTE]
> Деталі використання агентів в колекціях та документах дивіться ["Використання агентів в колекціях та документах"](server-platform/business-logic.md)


## Агент інтеграцій

