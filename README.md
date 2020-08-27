# Содержание  

* [Вопросы](#вопросы)
  * [Метрики QA](#метрики-qa)
    * [Test coverage](#test-coverage)
    * [Test automation coverage](#test-automation-coverage)
    * [Defect containment](#defect-containment)
    * [Точность оценки времени](#точность-оценки-времени)
    * [Прозрачность процесса](#прозрачность-процесса)
  * [Критерии качества](#критерии-качества)
  * [Критерии внедрения автоматизированного тестирования](#критерии-внедрения-автоматизированного-тестирования)
* [Техническое задание](#техническое-задание)
  * [Модель тестирования](#модель-тестирования)
    * [Алгоритм регистрации](#алгоритм-регистрации)
      * [Сайт](#сайт)
      * [Сторонние ресурсы](#сторонние-ресурсы)
      * [Панель администратора](#панель-администратора)
    * [Сценарии](#сценарии)
      * [Сайт](#сайт)
      * [Панель администратора](#панель-администратора)
      * [Сторонние ресурсы](#cторонние-ресурсы)
        * [Gmail](#gmail)
        * [LinkedIn](#linkedin)
        * [Slack](#slack)
        * [SSO](#sso)
  * [Автоматизация](#автоматизация)
* [Результаты](#результаты)
  * [Форма регистрации на сайте](#форма-регистрации-на-сайте)
  * [Подтверждение аккаунта](#подтверждение-аккаунта)
  * [Невозможно выслать инвайт на адрес зарегистрированного пользователя](#невозможно-выслать-инвайт-на-адрес-зарегистрированного-пользователя)
  * [Отсутствие антиспама или двухфакторной авторизации](#отсутствие-антиспама-или-двухфакторной-авторизации)
  * [Разная логика валидации одних и тех же полей профиля](#разная-логика-валидации-одних-и-тех-же-полей-профиля)
  * [Отсутствует валидация поля с номером телефона](#отсутствует-валидация-поля-с-номером-телефона)
  * [Некорректная валидация почтового адреса](#некорректная-валидация-почтового-адреса)
  * [Деактивация аккаунта](#деактивация-аккаунта)
    * [Некорректное сообщение об ошибке при деактивации](#некорректное-сообщение-об-ошибке-при-деактивации)
    * [После деактивации невозможно авторизоваться через Gmail или LinkedIn](#после-деактивации-невозможно-авторизоваться-через-Gmail-или-LinkedIn)
  * [Локализация](#локализация)
* [Дополнительно](#дополнительно)

# Вопросы
## Метрики QA
Надеюсь, что понял правильно и речь именно о метриках в контексте KPI.

### Test coverage
Наличие тест-кейсов(или чек-листов) на все функциональные блоки или требования к продукту.

### Test automation coverage
Процент покрытия автоматизированными тестами. Покрытие должно расти, сроки на проведение регрессионных тестов уменьшаться.

### Defect containment
В оригинале это отношение числа дефектов, обнаруженных до релиза к общему числу дефектов(баги найденные в процессе предрелизной подготовки, а также баги, которые были онаружены на продуктиве и являются следствием релиза). 

Ориентиром в работе должен быть результат, когда после релиза на бою отсутствуют дефекты критического и блокирующего характера.

### Точность оценки времени
Предсказуемые и точные прогнозы по времени(и завершению) работ по тестированию.

### Прозрачность процесса
Прозрачность процесса для всех участников, текущий статус и своевременная обратная связь от QA-команды.

## Критерии качества
Я ориентируюсь на контекст, так как критерии качества разные в зависимости от проекта, этапа его развития, специфики бизнеса и даже конкретного релиза.

## Критерии внедрения автоматизированного тестирования
Как и любое предприятие оно должно быть рентабельным. Рассчет рентабельности производится исходя следующих условий:

* Скорость выполнения тестов
* Частота выполнения тестов(какова продолжительность релизного цикла)
* Исключение человеческого фактора(репутационные и финансовые риски в случае сбоя)
* Стабильность покрываемой тестами функциональности(это влияет на то, сколько ресурсов необходимо на поддержку тестов)
* Сколько ресурсов нужно на создание тестов

# Техническое задание
## Модель тестирования

Заранее оговорюсь, что до уровня сценариев(некоторые из которых необходимо уже дробить до уровня тест-кейсов) расписан только блок регистрации на сайте. По сути тут представлен скелет модели тестирования с описанием направлений, которые необходимо прорабатывать, но думаю, что этого будет достаточно для того, чтобы уловить концепцию. Полный вариант с кейсами стоит по времени около 3 дней(фулл-тайм), на данный момент не располагаю подобным ресурсом времени.

### Алгоритм регистрации
Процесс регистрации пользователя может быть из трех источников:

1. Публичная часть сайта
2. Панель администратора
3. Авторизация через сторонние ресурсы
   * Gmail
   * LinkedIn
   * Slack
   * SSO

Во всех случаях приложение получает данные профиля(из формы на сайте или от стороннего ресурса) и производит процесс регистрации пользователя. В случае с регистрацией через административную панель(отправка инвайта) необходимо подтвердить аккаунт, перейдя по ссылке из письма. 

Процесс поэтапный, успешное прохождение каждого шага является условием для выполнения следующего.

#### Сайт

1. Валидация данных
2. Валидация принадлежности аккаунта к уже зарегистрированному домену
3. Предоставление доступа к административной панели

#### Панель администратора

1. Валидация данных
2. Отправка письма
3. Валидация подтверждения аккаунта
4. Предоставление доступа к административной панели исходя из прав доступа

#### Сторонние ресурсы

1. Авторизация на стороннем ресурсе
2. Предоставление доступа к административной панели

### Сценарии
#### Сайт
##### Ошибка валидации данных
###### Email
| ID | Сценарий | Комментарий |
|------------------|---------------|---------------|
| 1.1 | Пустое значение | |
| 1.2 | Пробел | |
| 1.3 | Минимальная длина почтового адреса | Значение < допустимой длины на один символ |
| 1.4 | Максимальная длина почтового адреса | Значение > допустимой длины на один символ |
| 1.5 | Формат почтового адреса | Корректная длина, но неверный формат |
| 1.6 | Email уже существующего подтвержденного аккаунта | |
| 1.7 | Email уже существующего неподтвержденного аккаунта | |
| 1.8 | Email заблокированного домена | Попытка зарегистрироваться с почтой на доменах для спама |
| 1.9 | Email администратора деактивированного аккаунта | |
| 1.10 | Email деактивированного аккаунта без админских прав | |
| 1.11 | Email аккаунта, привязанный к профилю на внешнем ресурсе и через который была авторизация | |

###### Имя и фамилия
| ID | Сценарий | Комментарий |
|------------------|---------------|---------------|
| 2.1 | Пустое значение | |
| 2.2 | Пробел | |
| 2.3 | Минимальная длина | Значение < допустимой длины на один символ |
| 2.4 | Максимальная длина | Значение > допустимой длины на один символ |
| 2.5 | Формат | Должно быть две строки, разделенных пробелом |

###### Компания
| ID | Сценарий | Комментарий |
|------------------|---------------|---------------|
| 3.1 | Пустое значение | |
| 3.2 | Пробел | |
| 3.3 | Минимальная длина | Значение < допустимой длины на один символ |
| 3.4 | Максимальная длина | Значение > допустимой длины на один символ |

###### Должность
| ID | Сценарий | Комментарий |
|------------------|---------------|---------------|
| 4.1 | Пустое значение в поле для ручного  ввода должности | |
| 4.2 | Пробел в поле для ручного  ввода должности | |
| 4.3 | Должность не выбрана | В списке указана опция «Выбрать» |

###### Номер телефона
| ID | Сценарий | 
|------------------|---------------|
| 5.1 | Пустое значение |
| 5.2 | Пробел |
| 5.3 | Невалидный номер телефона |

##### Успешная регистрация
| ID | Сценарий |
|------------------|---------------|
| 6.1 | Почта на обычном домене |
| 6.2 | Должность выбрана из списка |
| 6.3 | Должность указана вручную |
| 6.4 | Регистрация в существующий домен |

#### Панель администратора

Принцип тот же, что и с регистрацией на сайте, но стоит обратить особое внимание на корректность проставления прав для пользователя, который получает инвайт. Также стоит учесть кейсы с возможным протуханием ссылки и невалидным токеном в URI.

#### Сторонние ресурсы

Стоит обратить внимание на следующие моменты:

* Авторизация с аккаунтом, где основной «Email» в профиле совпадает с тем, что уже есть на сайте
* Корректность миграции данных профиля из внешней системы на сайт
* Права доступа к атрибутам внешнего профиля(в зависимости от стороннего ресурса и настроек аккаунта сайт может не иметь доступа к тем или иным атрибутам)
* Негативные сценарии авторизации, когда пользователь не залогинился на внешнем ресурсе
* Последовательная авторизация через разные внешние ресурсы, в профилях которых один и тот же «Email»

## Автоматизация

Автоматизировать совершенно точно имеет смысл процесс регистрации через сайт и админку. С остальными способами будут технические трудности, в том числе связанные с системами обнаружения подозрительной активности у сторонних ресурсов.

# Результаты

В процессе исследования логики работы регистрации возникли некоторые вопросы, уточнения, а также предложения по улучшению.

## Форма регистрации на сайте

При регистрации через форму на сайте все поля обязательны к заполнению, при этом логика валидации последовательная, тоесть если отправить пустую форму, то пользователь увидит только сообщение об ошибке в поле «Email» и о необходмости заполнения других полей информации не будет. Почему бы не подсвечивать как-то все обязательные поля?

## Подтверждение аккаунта

Выглядит так, что как таковой функциональности подтверждения почтового адреса нет, что странно выглядит для меня как для конечного пользователя. После регистрации сразу попадаешь в административную панель. Не нашел способа задать пароль, кроме как ресетнуть его через [форму](https://watch.appfollow.io/forgot) уже после регистрации.

## Невозможно выслать инвайт на адрес зарегистрированного пользователя

В административной панели при попытке добавить пользователя с почтовым адресом, который уже есть в системе получаю ошибку о том, что данный «Email» уже зарегистрирован. На сколько я понимаю, речь про инвайт, а не про нового пользователя в системе, почему нельзя пригласить уже существующего?

## Отсутствие антиспама или двухфакторной авторизации

По-хоршему, нужно сделать что-то из двух.

## Разная логика валидации одних и тех же полей профиля

При регистрации поле «Должность» обязательно(нельзя оставить в форме значение «Выбрать» и успешно зарегистрироваться), а поле с именем и фамилией должно содержать две строки, разделенные пробелом. В [профиле](https://watch.appfollow.io/settings/general) же можно сохранить «Должность» со значением «Other», а имя и фамилию строкой без пробелов.

## Отсутствует валидация поля с номером телефона

При регистрации на сайте номер телефона никак не валидируется на формат. Странно кстати, так и не смог нигде найти его в [профиле](https://watch.appfollow.io/settings/general).

## Некорректная валидация почтового адреса

При регистрации на сайте можно указать почту с кириллицей до знака «@».

## Деактивация аккаунта

Исследовал возможность регистрации с адресом почты деактивированного аккаунта(хотел понять логическое или физическое удаление происходит), но наткнулся на проблему.

### Некорректное сообщение об ошибке при деактивации

Шаги воспроизведения:

1. Регистрируемся(авторизовывался с помощью Gmail аккаунта [rbudorin@gmail.com](rbudorin@gmail.com))
2. Переходим в раздел деактивации
3. Указываем в поле с причиной деактивации «Test»
4. Нажимаем «Confirm Deactivate Account»

Ожидаемый результат: получем сообщение об ошибке, которое в явном виде сообщает пользователю о том, что причина деактивации должна быть более подробной

Фактический результат: получем сообщение об ошибке «Provide a reason, please.»

Если указать «Test and some letters.» в поле с причиной деактивации, то она происходит успешно: происходит редирект на страницу [логина](https://watch.appfollow.io/settings/deactivate) с соответствующим сообщением «This account was disabled.
Contact us if you have any questions on that matter.».

### После деактивации невозможно авторизоваться через Gmail или LinkedIn

У менять есть аккаунт Gmail, с которым я авторизовался ранее в системе. Этот адрес почты также указан в профиле LinkedIn. Так вот после того, как я деактивировал аккаунт в предыдущем шаге невозможно авторизоваться в системе и нет никаких сообщений об ошибке. 

Если на странице [логина](https://watch.appfollow.io/) кликнуть на иконку Gmail страница перезагружается и ничего не происходит, если нажать на кнопку LinkedIn, то повисает запрос на авторизацию в LinkedIn. Что тут считать корректным поведением неясно, либо нужно показывать сообщение о том, что аккаунт деактивирован, либо создавать новый пустой и пускать туда пользователя.

## Локализация

Регистрация [происходит](https://appfollow.io/ru) в русской локали, при этом интерфейс админки на английском. Вероятно это связано с тем, что у меня английская локаль на ноуте. Странно, тем не менее.

# Дополнительно

Если есть желание посмотреть на код и выполнение более «академической» задачи с точки зрения тест-дизайна, то можно сделать этот вот [тут](https://github.com/rbudorin/borrower-scoring). Это тестовое задание, которое я выполнял перед трудоустройством в [Яндекс.Практикум](https://praktikum.yandex.ru/) на роль наставника(факультет тестирования). 

Задание состоит из трех частей:

1. Составить модель тестирования алгоритма кредитного скоринга
2. Реализовать алгоритм скоринга исходя из входных параметров клиента
3. Покрыть unit-тестами
