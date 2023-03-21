# Абстрактні документи

Кожен документ бізнес-логіки повинен наслідувати відповідний абстрактний документ. Абстрактні документи декларують властивості, які необхідно описати та методи, які необхідно створити. 

| Документ | Призначення | Функціонал, який необхідно задати |
| :------: | :---------: | :--------------- |
| `application-list` | Реєстрація серверних застосунків | - |
| `application` | Реєстрація колекцій до застосунку | - |
| `collector` | Реєстрація документів до колекції | - Визначити кожний з документів (який потенційно може входити до колекції) - присвоїти конкретний документ або присвоїти `undefined`. <br/> - Визначити назву колекціонера `collector`. <br/> - Створити `Set` колекцію, при необхідно створення словників мов. |
| `router` | Реєстрація маршрутів | - Визначити шляхи маршрутів. <br/> - Ініціалізувати `Set` колекцію для створення повноцінного опису маршрутів. |
| `controller` | Реєстрація основних обробників | - |
| `middleware` | Реєстрація проміжних обробників | - |
| `repository` | Опис обробників запитів до бази даних | - Визначити назву сутності таблиці `entity`. |
| `entity` | Опис схеми сутності таблиці в базі даних | - Визначити назву сутності таблиці `entity`. <br/> - Визначити схему сутності таблиці |
| `dictionary` | Опис словника конкретної мови колекції | - Визначити словник, який складається з ключа та перекладу. <br/> - Описати мову словника |
| `swagger` | Опис Swagger документації API колекції | - Визначити тег колекції. <br/> - Визначити опис маршрутів |
| `asyncapi` | Опис AsyncApi документації API колекції | - Визначити тег колекції. <br/> - Визначити опис подій |
| `validator` | Опис схем валідації колекції | - |

Абстрактні документи, декларативно записують функціонал бізнес-логіки до загальної схеми, а також надають функціонал по роботі з агентами.

| Документ | Функціонал, який необхідно задати |
| :------: | :--------------- |
| `application-list` | - |
| `application` | - |
| `collector` | - |
| `router` | - агент базових операцій `base operations`. |
| `controller` | - агент бізнес-логіки `business logic agent`. <br/> - агент інтеграцій `integration agent`. <br/> - агент базових операцій `base operations`. |
| `middleware` | - агент бізнес-логіки `business logic agent`. <br/> - агент інтеграцій `integration agent`. <br/> - агент базових операцій `base operations`. |
| `repository` | - агент базових операцій `base operations`. |
| `entity` | - агент базових операцій `base operations`. |
| `dictionary` | - агент базових операцій `base operations`. |
| `swagger` | - агент базових операцій `base operations`. |
| `asyncapi` | - агент базових операцій `base operations`. |
| `validator` | - агент бізнес-логіки `business logic agent`. <br/> - агент базових операцій `base operations`. |
