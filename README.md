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
### Алгоритм регистрации
Процесс регистрации пользователя может быть из трех источников:

1. Публичная часть сайта
2. Панель администратора
3. Авторизация через сторонние ресурсы
   * Gmail
   * LinkedIn
   * Slack
   * SSO

Во всех случаях приложение получает данные профиля(из формы на сайте или от стороннего ресурса) и производит процесс регистрации пользователя. В случае с регистрацией административную панель(отправка инвайта) необходимо подтвердить аккаунт, перейдя по ссылке из письма. 

Процесс поэтапный, успешное выполнение каждого шага является условием для выполнения следующего.

#### Сайт

1. Валидация данных
2. Валидация принадлежности аккаунта у уже зарегистрированному домену
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
| 1.8 | Email заблокированного домена | |

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
| 3.3 | Максимальная длина | Значение > допустимой длины на один символ |

###### Должность
| ID | Сценарий | Комментарий |
|------------------|---------------|---------------|
| 4.1 | Пустое значение в поле для ручного  ввода должности | |
| 4.2 | Пробел в поле для ручного  ввода должности | |
| 4.3 | Должность не выбрана | |

##### Успешная отправка письма
| ID | Сценарий | Комментарий |
|------------------|---------------|---------------|
| 4.1 | Почта на кириллическом домене | |
| 4.2 | Почта на обычном домене | |
| 4.3 | Должность выбрана из списка | |
| 4.3 | Должность указана вручную | |
| 4.3 | Регистрация в существующий домен | |

##### Подтверждение аккаунта

#### Панель администратора
#### Gmail
#### LinkedIn
#### Slack
#### SSO
## Автоматизация


локализация
сеть
совместимость

# Результаты

В процессе исследования логики работы регистрации возникли некоторые вопросы, уточнения, а также предложения по улучшению.

## Форма регистрации на сайте

При регистрации через форму на сайте все поля обязательны к заполнению, при этом логика валидации последовательная, тоесть если отправить пустую форму, то пользователь увидит только сообщение об ошибке в поле «Email» и о необходмости заполнения других полей информации не будет. Почему бы не подсвечивать как-то все обязательные поля?

## Подтверждение аккаунта

Выглядит так, что как таковой функциональности подтверждения почтового адреса нет, что странно выглядит для меня как для конечного пользователя. После регистрации сразу попадаешь в административную панель. Не нашел способа задать пароль, кроме как ресетнуть его через [форму](https://watch.appfollow.io/forgot) ужде после регистрации.

## Невозможно выслать инвайт на адрес зарегистрированного пользователя

В административной панели при попытке добавить пользователя с почтовым адресом, который уже есть в системе получаю ошибку о том, что данный «Email» уже зарегистрирован. На сколько я понимаю, речь про инвайт, а не про нового пользователя в системе, почему нельзя пригласить уже существующего?

## Отсутствие антиспама или двухфакторной авторизации

По-хоршему, нужно сделать что-то из двух.

## Разная логика валидации одних и тех же полей профиля

При регистрации поле «Должность» обязательно(нельзя оставить в форме значение «Другое» и успешно зарегистрироваться), а поле с именем и фамилией должно содержать две строки, разделенные пробелом. В [профиле](https://watch.appfollow.io/settings/general) же можно сохранить «Должность» со значением «Другое», а имя и фамилию строкой без пробелов.
