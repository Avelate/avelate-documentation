# Адаптери

Адаптери `adapters` призначені для адаптації загальних схем до відповідного фреймворку чи протоколу.  З першого погляду адаптери мають ідентичне завдання як і стратегії, але це не так. 

Стратегії реалізовують один і той же інтерфейс використовуючи різні технології однієї області призначення. Адаптри перетворють загальну схему в один і той ж інтерфейс використовуючи різні технології однієї області призначення. Простими словами стратегії створюються аналог з нуля, адаптери перетворються абстракцію під аналог.


## Адаптери протоколів

Наразі сервер підтримує HTTP та Websocket протоколи. При потребі, в ході подальшого розвитку, можуть бути додані інші протоколи, такі як, TCP, UDP чи ін. HTTP протокол використовує стандартну бібліотеку - [http](https://nodejs.org/api/http.html), а Websocket протокол - бібліотеку - [ws](https://www.npmjs.com/package/ws). 

Кожний протокол працює на власному порту, який вказується в конфігураційному файлі `transport.json` та виконує лише одну функцію - створює веб-сервер відповідного порту.

<<SCHEMA>>

Приклад HTTP протоколу:

```typescript
@injectable()
class HttpProtocolAdapter extends AbstractProtocolAdapter implements IHttpProtocolAdapter {
  protected _ADAPTER_NAME = HttpProtocolAdapter.name;
  private _business: express.Express;
  private _httpServer: http.Server;

  constructor(
    @inject(CoreSymbols.ConfigurationService) private _confService: IConfigurationService,
    @inject(CoreSymbols.LoggerService) protected _loggerService: ILoggerService
  ) {
    super();
  }

  public setFrameworkInstance(object: express.Express): void {
    this._business = object;
  }

  private _createServer(): void {
    const protocol = this._confService.getMandatory<string>('transport:http:protocol');
    const host = this._confService.getMandatory<string>('transport:http:host');
    const port = this._confService.getMandatory<number>('transport:http:port');

    this._httpServer = http.createServer(this._business);
    this._httpServer.listen(port, () => {
      this._loggerService.system(
        `Http protocol is listening on ${protocol}://${host}:${port}.`,
        'transport'
      );
    });
  }

  protected _init(): void {
    if (this._confService.isStarted) {
      this._createServer();
    } else {
      this._confService.once(EventNames.CONF_START, () => {
        this._createServer();
      });
    }
  }
}

export default HttpProtocolAdapter;
```

Окремо варто замітити, що всередину сервера окремо поміщається об'єкт бізнес-логіки. 

## Адаптери фреймворків

Адаптери фреймворків відповідають за перетворення загальної схеми бізнес-логіки в зрозумілий контракт взаємодії бізнес-логіки з конкретним фреймворком. Наразі серверне ядро підтримує 2 варіанта фреймворків:
- `express`
- `fastify`

Кожний фреймворк описує одні й ті ж можливості та потреби:
- робота з `cors`
- організація документації API з однаковим маршрутом доступу. 
- організація локалізацій.
- створення незалежного виклику того чи іншого API маршруту.
- та ін.

<<<SCHEMA>>>

Кожний адаптер фреймворку використовує змінні оточення, які описуються в конфігураційному файлі `business.json`.

## Розвиток та підтримка 

> [!TIP]
> Кожний адаптер `adapter` відповідної області призначення успадковується від абстрактного адаптера `abstract adapter` цієї області призначення. 
> При потребі створення нового адаптера, уже існуючої описаної області призначення в якій наявний абстрактний адаптер, новий адаптер повинен бути унаслідований від абстрактного адаптера.
> При потребі розширення чи змін в існуючих адаптерах, якщо ці зміни стосуються публічних методів, то ці методи повинні бути описані в абстрактному адаптері відповідної області призначення. Назва метода повинна бути синонімом від старого метода, якщо це оновлена версія старого метода чи повинна мати відповідний опис метода, в випадку нового функціоналу.
> При потребі створення нової області призначення з створенням нового адаптера в цій області, повинний бути створений абстрактний адаптер цієї області призначення та описаний адаптер конкретної реалізації. 
> В випадку існуючої області призначення, новий адаптер повинен бути доданий в відповідну фабрику, якщо ж створюється і нова область призначення, повинна бути створена і нова фабрика, яка має назву аналогічну назві області призначення. 