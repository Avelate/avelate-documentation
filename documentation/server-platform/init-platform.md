# Ініціалізація серверної платформи

Модуль `server.ts` є точкою входу до серверної платформи. В ньому з контейнера інверсії залежностей, дістається екземпляр
класу `ServerInitor`. <br/>
У якого є два методи:
- `start` - створює з'єднання з елементами платформи 
- `stop` - зупиняє з'єднання з елементами платформи

Та які реалізовані наступним чином:

```typescript
const server = ioc.get<IServerInitor>(CoreSymbols.ServerInitor)

const start = async () => {
    await server.start()
}

const stop = async () => {
    await server.stop()
    process.removeAllListeners()
    process.exit(0)
}

process.on('SIGTERM', stop)
process.on('SIGINT', stop)
process.on('SIGHUP', stop)
process.on("uncaughtException", (e) => {
    console.error(e)
    server.stop().then(() => {
        process.exit(1);
    });
});

// ...

start().catch((e) => {
    console.log("Server end with error: ", e);
});
```
<br/>

Запуск серверної платформи складається з ініціалізації елементів платформи. Зупинка платформи може виникнути в процесі виникнення
помилок програми в ході створення відповідного сигналу, а якщо ні один обробник не відловив помилку та не здійснив виключення, то на цей
рахунок приймає роль `uncaughtException` тип події.