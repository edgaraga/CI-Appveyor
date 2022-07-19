[![Build status](https://ci.appveyor.com/api/projects/status/vpejhcsy5avwgc7w/branch/main?svg=true)](https://ci.appveyor.com/project/Gnucheva/ci-appveyor/branch/main)

Общая схема работы выглядит следующим образом: CI должен запустить целевой сервис в фоновом режиме (который вы и тестируете) и ваши авто-тесты. Для этого мы будем на этот раз использовать возможности Bash.    
Для того, чтобы запустить целевой сервис есть несколько вариантов, самый простой из которых - положить jar-файл прямо в ваш репозиторий. Когда AppVeyor будет выкачивать исходники авто-тестов, он выкачает и ваш сервис.    
Ваш целевой сервис (SUT - System under test), расположен в файле `app-mbank.jar` (в проекте с лекции). Вам нужно его положить в каталог `artifacts` вашего проекта (создайте его).   
Поскольку файлы с расширением `.jar` находятся в списках `.gitignore`б вам нужно принудительно заставить git следить за ними: `git add -f artifacts/app-mbank.jar`.    
После чего сделать `git push`. Обязательно удостоверьтесь, что файл попал в репозиторий.   


Что нужно сделать:

#### Шаг 1. Добавить зависимость

```groovy
dependencies {
    testImplementation 'io.rest-assured:rest-assured:4.3.0'
    testImplementation 'io.rest-assured:json-schema-validator:4.3.0'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.6.1'
}
```

#### Шаг 2. Сохраните схему в ресурсах

Создайте каталог `resources` в `src/test` и поместите туда схему (не забудьте удалить комментарии).
#### Шаг 3. Включить проверку схемы

Модифицируйте существующий тест так, чтобы он проверял соответствие схеме. Для этого:

```java
      // код теста
      .then()
          .statusCode(200)
          // static import для JsonSchemaValidator.matchesJsonSchemaInClasspath
          .body(matchesJsonSchemaInClasspath("accounts.schema.json"))
      ;
```

Удостоверьтесь, что тесты проходят при соответствии ответа схеме и падают, если вы поменяете что-то в схеме (например, тип для `id`)

#### Шаг 4. Доработать схему

Изучите документацию на тип [`object`](https://json-schema.org/understanding-json-schema/reference/object.html) и найдите способ валидации значения поля на два из возможных значения: "RUB" или "USD".

Доработайте схему соответствующим образом, удостоверьтесь, что тесты проходят (в том числе в CI).

Поменяйте "RUB" на "RUR" и удостоверьтесь, что тесты падают (в том числе в CI).

