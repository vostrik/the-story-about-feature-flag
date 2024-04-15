# История одного фича-флага

## TODO
- [x] добавить про таблицу истинности
- [ ] перерисовать картинку хореография vs оркестрация
- [x] Общая структура: перейти на проблема -> визуализация решения -> код
    - уйти от описания роадмапа жизни продукта к сценариям фиче-флагов о которых поговорим
    - обратить внимание в начале докладе и в выводах: это один фиче-флаг и один ифчик, а сценарии использования разные
    - раскрытие сценария «добавить фиче-флаг под каждый микрофронт» — в чем отличие от общего флага
    - как удалять фиче-флаги
- [x] фиче-флаг для 2 команд разработки
    - нарисовать общую схему взаимодействия
- [x] как работают дефолты и удаленное хранилище
    - проблема: настройка пропала на сервисе / сервис упал / корпоративная сеть без доступа к интернету
    - проблема: хождение к сервису на каждое обращение к фф, нужна синхронная работа для лучшей скорости
    - добавить про кеши и удаленный формат работы
    - отдельно осветить бэкэндовую и фронтовую часть: в моей реализации core — фф backend, на фронтенде вызов библиотеки и пуши на обновление
    - как разделяются дефолты между пресетами
- [x] реализация функций из примеров
- [x] смена поколений Дизайн-системы
    - как происходит смена?
    - 2 npm пакета: shared зависимость
    - отдельный слой постройки форм
- [x] про тесты
    - как протестировать всю комбинаторику в юнитах
    - как протестировать в e2e: как пишем тесты с учетом включи фиче-флаг, выключи фиче-флаг
    - что случается, когда недозакрыли функциональность
- [x] стаднартные библиотеки и подходы
    - добавить специфичную фукнциональность для JS
- [ ] добавить qr код в последнем файле
- [x] 22 - кейс? до этого убрать user и тд 
- [ ] 29 - можно показать конкретный кейс, можно рассказать, почему флаги сразу в микрофронт не прокидывается
- [ ] 32 - уход в сторону, можно убрать
- [ ] 39 - надо визуализировать
- [ ] 43 - объединяем - показать пресеты
- [ ] 45 - задача 1: Решение - что можешь посоветовать из опенсурса
    - задача 3: не понял, причем тут фича флаги
- [ ] 49 - после обл провайдеров, может не подойти - почему может не подойти?
    - разработали либу - нет.
- [ ] 52 - можно показать пример теста
- [ ] 53 - баги в удаленном сервисе. Удаление - не про тесты. 
    - код тестов


## План
1. Расскажу о себе и несколько слов о проекте, которым я занимаюсь и в котором потребовались фиче-флаги (фф).
2. Короткое овервью что такое фф, какую задачу они решают.
3. Задача 1: деплоймент в разные среды. Как решили перегрузку ифнраструктурного слоя для облака и для онпрема.
4. Задача 2: распределенная разработка. Как разрабатывали фичу в нескольких командах и активировали на проде.
5. Задача 3: быстрые релизы. Как релизились с мастера и закрывали фф неготовую функциональность.
6. Переключимся с нашего опыта на доступные в экосистеме решения. Рассмотрим, какие есть стандартные библиотеки и подходы.
7. Задача 4: миграция кодовой базы на новую мажорную версию UI Kit. Как спасает фф, когда мастер развалился, а до релиза рукой подать.
8. Задача 5: поставка трёх продуктов из единой кодовой базы. Какие плюсы и сложности приносят фф.
9. Выводы. Какие фф рассмотрели, какие сложности и как добавить тот самый 1 фф, чтобы править всеми другими фф.
10. Ссылка на материалы, обсуждение.

Хочу подать материал через сторителлинг и оттолкнуться от проблематики нашего пути длиной в последние 7 лет и те проблемы и решения, которые видели и решали.

## Большие и маленькие цели

### Большая цель
Я люблю веб-разработку и хочу быть адвокатом для хороших практик веб-разработки 

### Цель доклада
Тип цели: информационная/убеждающая — Показать конкретное решение конкретной задачи/кейса
Помочь слушателям решить несколько «болей» в разработке через фиче-флаги.

### Структура доклада
Структура доклада: Сценарная
Рассказ о развитии проекта, возникающих вызовах и способах решения.

## Слайд 1. О себе

Привет. Меня зовут Паша Востриков, я занимаюсь JS разработкой в Kaspersky. Делаю frontend, облачные сервисы на Node.js, self-hosted поставку, платформенные компоненты и библиотеки.

## Слайд 2. О чем доклад
Показать сценарии применения фиче-флагов для упрощения разработки
TODO: Сразу сделать пример кода

## Слайд 3. Agenda

1. Предыстория: Переход от UI на C++ к веб-приложению
    - Команда: 7 человек
    - Задача: Управлять корпоративной защитой из брайзера
    - Решение: Full Stak JS
2. Этап: Быстрый рост проекта: увеличение команды и скоупа работ (Задача 3: быстрые релизы)
    - Команда: 5 групп разработки по 5 человек
    - Задача: Поставлять фукнционал
3. Этап: Выделение зон ответственности команд (Задача 2: распределенная разработка)
    - Команда: 2 группы разработки 7 человек и 4 человека
    - Задача: Сделать общую фичу, декомпозировав работы на 2 отдела разработки — управление (знают как управлять) и защиты (знают как защищать)
    - Решение: сквозной фиче-флаг
4. Этап: Выход на новые рынки, защита из облака (Задача 1: деплоймент в разные среды)
5. Этап: Обновление технологий в долгоживущем продукте (Задача 4: миграция кодовой базы)
6. Этап: Формирование экосистемы: готовность защищать от цветочного ларька (SMB) до компании, работающей на всех континентах (Enterprise) (Задача 5: поставка трех продуктов из единой кодовой базы)
  Пример: доставка: доставка еды, курьерская доставка дверь-дверь внутри города, междугородняя доставка габаритных грузов 

## Слайд 9. Что такое ФФ и какую задачу они решают

> Фиче-флаги — это настройки, которые позволяют не трогая программистов менять поведение программы, к примеру включать и выключать фичи.

[Фёдор Борщёв](https://borshev.com/feature-flags/)

Фиче-флаги — это разновидность данных, используемых в системе. Нечто между стейтом и конфигом приложения.

Фиче-флаги нужны для:
1. A / B тестов
2. Canary релизов
3. Закрытия не готовой фичи в main ветке

Резюмируя, фиче-флаги нужны для включения и выключения функциональности при необходимости. А необходимости бывают разные.

Больше можно почитать на [martinfowler.com](https://martinfowler.com/articles/feature-toggles.html).

### Дисклеймер

1. Этот доклад про применения фиче-флагов в инженерных целях, не про a / b тесты, исследования и маркетинг.

2. Если есть возможность не ветвить реализации или написать сразу протестированную фичу без багов — сделайте это. Для всех остальных случае потребуются фиче-флаги.

## Слайд 10. High Level Architecture

Было: KSC MMC
Стало: KSC Web Console

## Слайд 14. Релиз, когда код не готов (Задача 3: быстрые релизы)

Команда становится больше, продукт больше, релизы становятся длиннее. Пока всё разработают и проверят. Даже авто-тесты, и их написание занимает много времени.

Но мы хотим релизиться часто. Мало того что часто, ритмично.

Вне зависимости от того готов ли функциональность, релиз должен выйти.

Итак, давайте решим следующую задачу.

Дано:
1. Частые релизы по готовности фичи, но не реже чем X времени.
2. Коротко живущие ветки: сделал атомарную таску — залей. Ни к чему эти PRы на 3 тысячи файлов и полгода работы.

Как этого достичь? Новая фича при разработке обарачивается в фиче-флаг. Он в main по-умолчанию выключен. При разработке, в автотестах, для ручного тестирования можно включить фиче-флаг для конкретного пользователя. Так даже Canary релиз на проде можно сделать:

## Слайд 24. Фиче-флаг для 2 команд разработки (Задача 2: распределенная разработка)

Приложение росло и появилась необходимость делать отдельные части фичи разными командами. Архитектура поставки — микрофронтенды и микросервисы, но речь не об этом.

Следующим этапом потребовалась выкатка фичи, разработанной тремя отдельными командами. Разумеется, каждая команда релизится в удобное для себя время и фича включается в Cloud поставке по готовности.

Простейший способ — это выставление env переменных в соответствующих сервисах.

Но что если улучшить? Например, HostApp должен контролировать поведение фичи и распространять включение централизованно? Такое может потребоваться, например, если мы управляем фичей из пользовательского интерфейса.

### Конфигмап со значениями фиче-флагов `./feature-flags.json`
```
{
  "FEATURE_WELCOME_WIZARD_ENABLED": 1
}
```

### Инициализация фиче-флагов в HostApp
```javascript
const { featureRegistry } = require('@kaspersky/feature-registry')

const { 
  isServerFeatureEnabled,
  isUserFeatureEnabled,
  getFeatureFlags
} = featureRegistry.init(global.config.featureFlagConfigMapPath)

const isWelcomeWizardEnabled = await isServerFeatureEnabled({
  featureName: 'WELCOME_WIZARD_ENABLED'
})
```

### Использование в микросервисе или микрофронтенде
```javascript
const { hostApp } = require('@kaspersky/feature-registry')

hostApp.isServerFeatureEnabled({
  featureName: 'WELCOME_WIZARD_ENABLED'
})
```

Это runtime фиче-флаг, контролирующий поведение в разных частях распределенной системы.

## Слайд 31. Переход из Self-hosted к Cloud Native (Задача 1: деплоймент в разные среды)

Итак, новая задача, с которой мы с командой столкнулись, еще не зная ни о каких фиче-флагах, деплоймент нашего приложения в 2 разных среды: Cloud и Self-Hosted.

В первом случае это Cloud Native поставка в k8s кластер нашего Node.js кода и фронтенда, а во втором случае — раскатка в инфраструктуре заказчика.

Такой формат поставки очень типичен для многих веб-приложений:

1. GitLab
2. Confluence
3. Sentry
4. Kaspersky Security Center

Мы делали Kaspersky Security Center — решение для управления корпоративной защитой. Пропатчевать операционную систему одной кнопкой, мониторить и автоматически реагигировать, когда корпоративную сеть ломают снаружи или изнутри.

Соответственно одно и то же Node.js приложение должно работать принципиально в 2 средах, контейнеры использовать в self-hosted мы не могли — не все заказчики к этому были готовы.

Решение — наш первый инфраструктурный фиче-флаг.

### Файловая структура:
```
.
└── src/
    ├── index.js
    └── feature/
        ├── env-local/
        │   └── feature.js
        ├── env-cloud/
        │   └── feature.js
        └── feature.common.js
```

### Стартовый файл проекта `src/index.js`: 
```javascript
const path = require('path')

const featureFilePath = path.resolve(
  __dirname,
  'feature',
  globl.config.targetFolder,  // <---- environment implementation folder
  'feature.js'
)

const { FeatureLogic } = require(featureFilePath)
FeatureLogic.init()
```

### Фича для облака `src/feature/env-cloud/feature.js`:

```javascript
const { CommonFeatureLogic } = require('../feature.common.js')

class FeatureLogic extends CommonFeatureLogic () {
  async init () {
    return 'I am Cloud feature!'
  }
}
```

Плюс на этапе сборки убрать избыточные для Cloud директории env-local.

Таким образом у нас еще нет централизованного управления фиче-флагами, но уже есть возможность управлять кодом для определенной поставки.

Это buildtime фиче-флаг.

## Слайд 36. Задача 4: миграция кодовой базы

Возвращаясь к продуктовым задачам. Мы разработали новую версию Дизайн-системы и нужно менять компонентную базу в живом продукте.

Разработкой продукта к этому моменту уже занимается 30 человек в нашем JS стриме и порядка 20 команд 2-4 разработчика в смежных отделах.

Задача поменять UI компоненты во фронте на 20 команд выглядит как «поменяйте крылья у летящего самолета».

Первый подход — глобальный фиче-флаг V6_UI_KIT_ENABLED. Начали катить на стейджинги и фиаско. На части микрофронтов взлетело, на части — нет. Виной тому и наши девелоперские ошибки, и edge кейсы и вообще не ошибается тот, кто ничего не делает. Не бойтесь ошибаться, главное делайте выводы.

Второй подход — быстро откатить фиче-флаг V6_UI_KIT_ENABLED. Осмыслить что пошло не так. Написать Smoke E2E тесты в добавок к написанным юнитам. Пока глобальный фиче-флаг, переключающий версию UI Kit, откатили, нужно предложить решение, которое разблокирует команды в отдельных сценариях. Добавляем фиче-флаг для перехода только отдельных разделов.

К этому моменту написаны Smoke тесты, подчищены всплывшие ошибки, идет раннее тестирование отдельных продуктовых разделов в разных командах. Мы готовы к следующему этапу — включение фиче-флага для отдельного микрофронта. Из 20 микрофронтендов 5 уже стабилизированы и включены в деплойменте по-умолчанию. Остальные включаются по мере тестирования.

Таким образом, мы перешли от глобального скозного фиче-флага к per-component флагам и без изменения кода может прыгать со старой реализации на новую и обратно, в случае появления проблем.

## Слайд 40. Формирование экосистемы: создание нескольких продуктов из единой кодовой базы (Задача 5: поставка трех продуктов из единой кодовой базы)

Количество пользователей продукта растет и есть потребность из единой кодовой базы делать 3 поставки:

1. Cloud (Public Cloud)
2. Self-hosted
3. Private-cloud

При этом, исходя из потребностей клиентов у нас 2 инфраструктуры: контейнеризованная и сервисы внутри ОС.

И три подмножества пользовательских сценариев: пересекающиеся процентов 80 и 20 процентов отличий на каждую поставку.

Инфраструктуру разруливаем buildtime фиче-флагом с начала моего доклада.

Пользовательские сценарии ветвим серверными фиче-флаги.

Новую функциональность тестируем per-user фиче-флагами.

Вроде бы все по полочкам, что может пойти не так?

Комбинационный взрыв, когда появляется простыня из десятков флагов и энв переменных и что относится к чему довольно сложно разобрать.

Тут на помощь приходят 2 довольно простых решения: сделать пресеты фиче-флагов для каждой из поставок + валидировать env-переменные builtime, чтобы не было угадайки а что куда выставить при разработке и тем более в проде.

Для этого отлично подошла библиотека envaild.



## Слайд 42. Стандартные библиотеки и подходы

1. Когда мы говорим про подходы, конечно начать можно с if и env переменных / запроса к бэкенду.
2. Env переменные трансформируются в ConfigMap (json / yaml).
3. А ConfigMap переходит в кеширующий сервис, который хранит настройки локально и умеет забирать их / принимать обновления с сервера.
4. Последняя важная мастовая часть — это возможность применять фиче-флаг на группы: серверов / регионов / пользователей.

Когда мы говорим про библиотеки и сервисы:
1. Конечно же нужно упомянуть Firebase и JS SDK
2. Unleash
3. featurevisor
4. Конечно, многие облачные провайдеры предоставляют из коробки сервис для работы с фиче-флагами: 

  - AWS AppConfig
  - Microsoft Azure App Configuration
  - Google Cloud Firestore

## Слайд 44. Выводы

Подводя итог, мы рассмотрели

1. Фиче-флаг для Work in Progress кода. Позволяет выкатывать ранние canary релизы и заниматься отладкой неготовой фичи.
2. Фиче-флаг для 2 команд разработки. Позволяет управлять функциональностью сквозь все части системы, в том числе при распределенном деплое.
3. Применение фиче-флагов на отдельные группы пользователей или серверов для тестирования фичи.
4. Разбитие одного общего фиче-флага на подгруппы для градуипрованного раскатывания нового фичи в большом приложении.
5. Buildtime перегрузка подсистем. (Не фиче-флаг, но тоже полезно)

## Слайд 14. Ссылка на материалы

TBD
