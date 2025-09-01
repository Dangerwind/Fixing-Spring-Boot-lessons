мои сохраненные решения: https://ru.hexlet.io/code_reviews?q%5Buser_id_eq%5D=873990

___



# Fixing-Spring-Boot-lessons
some notations for Spring Boot lessons

**Общая верстка всей теории и упражнений**
   
Весь текст находится по центру экрана и примеры не помещаются даже если разрешение монитора позволяет (обычное 4K разрешение), приходится скролить вправо-влево.
Зачем так сделано - непонятно. Я хотел бы испольовать весь экран, но приходится все читать из небольшой центральнйо колонки.
   
[Скрин экрана с окном на весь экран, информация только в центральнйо колонке. Пример кода не влезает в эту колонку.](https://github.com/Dangerwind/Fixing-Spring-Boot-lessons/blob/main/image/longscreen01.png)

___   

**Знакомство с Spring Boot**
> Выполните эти команды в вашей домашней директории
> 
> mkdir spring-example
> 
>cd spring-example
> 
>...
> 
>Enter selection (default: basic) [1..4]
> 
>...  

было бы хорошо показать какие цифры выбирать или написать, что выбрать значение по умолчанию.
    
___
      
> Например, стартер web добавляет Tomcat и Spring MVC

Выше нигде не сказано, что такое Tomcat и Spring MVC

В упражнении 
```
    @GetMapping("/")
    String home() {
        return "Welcome to Hexlet!!";
    }
```

Хорошо бы поставить `public String home() {...` чтобы явно показать, что это открытый эндпоинт, хотя это и не критично но мне кажется хороший тон писать модификаторы доступа.
   
___
   
**Аннотации**
```
@AllArgsConstructor
@RequestMapping("/api")
public class PostsController {

    @Autowired
    private final PostRepository repository;
.......
```
тут @Autowired и @AllArgsConstructor делают одно и тоже, наверное стоит `@Autowired` убрать 
   
___
   
>Добавим в @LogExecutionTime два параметра. Первый временно выключит логирование, а второй задаст минимальное время выполнения, ниже которого логировать не нужно:

Где прописано как enabled() отключает логгирование и threshold() деалет пороговое время? Нет кода этих анотации. Наверное стоит указать, что сам код мы пока что не приводим для упрощения.
А то создается впечатление, что просто указав enabled() мы волшебным путем отключаем логгирование и про threshold() тоже самое.
   
___
    
**HTTP и CRUD приложения**
```
    if (maybePage.isPresent()) {
        var page = maybePage.get();
        page.setSlug(data.getSlug());
        page.setName(data.getName());
        page.setBody(data.getBody());
    }
```
такая проверка `if (maybePage.isPresent())` и .get() считается не очень хорошим вариантом. Лучше добавить в стрим `.orElseThrow(() -> new RuntimeException("Page not found"));` вместо if.
    
___
   

```
    @PutMapping("/pages/{id}") // Обновление страницы
    public Page update(@PathVariable String id, @RequestBody Page data) {
        var maybePage = pages.stream()
            .filter(p -> p.getSlug().equals(id))
            .findFirst();
        if (maybePage.isPresent()) {
            var page = maybePage.get();
            page.setSlug(data.getSlug());
            page.setName(data.getName());
            page.setBody(data.getBody());
        }
        return data;
    }
```
Тут похоже опечатка, мы принимаем data и потом его же и возвращаем, page изменяется под эту data и они в и тоге одинаковые, но логичне возвращать то что мы изменили а не входные данные, те page может быть и не полностью изменена, если были какие то условия модицикации ! наверное надо  внутри if (maybePage.isPresent()) {....... return page; } а в конце выбросить исключение throw new RuntimeException("Page not found");.
или лучше вообще перпеписать так:
```
        var maybePage = pages.stream()
            .filter(p -> p.getSlug().equals(id))
            .findFirst()
            .orElseThrow(() -> new RuntimeException("Page not found"));

            maybePage.setId(data.getId());
            maybePage.setTitle(data.getTitle());
            maybePage.setBody(data.getBody());
            return maybePage;
```
   
___
   
**REST API в Spring Boot**

Тесты
>Где в структуре проекта должен находиться контроллер, реализующий маршрут /api/courses/{id}/lessons?

правильный ответ 
>  src/main/java/project/controller
>
>  └── courses
>
>      └── LessonsController.java

но в теории сказано что `Префикс /api` то есть правильный вариант должен быть:
>src/main/java/project/controller/api
>
>  └── courses
>
>      └── LessonsController.java

___

Вопрос: 3 из 3. 
> Допустим, в конфигурационном файле приложения есть такой код:
>
>config:
> 
>  users:
> 
>    names:
> 
>      - John
> 
>      - Marta
> 
>Допишите код класса так, чтобы получить доступ к именам пользователей.

В теории нигде не сказано что users: names: надо записывать в @ConfigurationProperties(prefix = как users.names через точку
___

**Конфигурация**

Упражнение

По заданию не совсем понятно откуда берутся имена админов. Наверное стоит написать что они в списке всех пользователей. 

___

**Спецификация JPA Entity**

В упражнении дан

```
curl --request POST \
  --url http://localhost/api/people \
  --header 'Content-Type: application/json' \
  --header 'User-Agent: insomnia/2023.5.8' \
  --data '{
  "firstName": "John",
  "lastName": "Doe"
}'

Ответ:
...
```
Но мы же не можем запустить из упражнения, потому проверить это нет возможности. Это стоит убрать.

___

**Обработка ошибок**
   
В упражнении файл GlobalExceptionHandler.java

там где все import хорошо бы добавить // BEGIN //END чтобы было понятно что туда надо добавить импорт нашего исключения `import io.hexlet.exception.ResourceNotFoundException;`
а то не сразу понятно что он не добавлен и его надо отдельно прописать.

___

**Сравнение сущностей**

в упражнении
   
`Запуск приложения` и `Подсказки` не имеют смысла так как это тренажер и нельзя запустить код! Следует убрать!

___
 
**Аудит в JPA**

Тоже самое, `Запустите приложение и создайте несколько задач. Убедитесь, что поля createdAt и updatedAt автоматически заполняются` - из тренажера нельзя запустить прилодение
    
надо или добавить в import 
```
import org.springframework.data.jpa.domain.support.AuditingEntityListener;
import jakarta.persistence.GeneratedValue;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
```
или в import написать //BEGIN //END чтобы суденты сами import написали а то сразу непонятно надо писать или уже добавлено

при проверке явных ошидок не выдает но `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`

```
make: *** wait: No child processes.  Stop.
make: *** Waiting for unfinished jobs....
make: *** wait: No child processes.  Stop.
```

в Makefile убоал  -o -q и тесты прошли

```
test:
	@$(MVN) test -o -q
```

Получается что задание пройти невозможно.
   
___
    
**Возможности JPA Repository**
    
Тоже самое, убрать все про запуск приложения.
    
и так же не может завершится `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`
Даже если код правильно написан.
    
В задании ниже не написано что надо написать в application.yml а файл есть и там есть //BEGIN //END
   
___


**Инверсия зависимостей**

   Тоже самое, убрать все про запуск приложения  и так же не может завершится

Линтер ругается на неиспользуемые import в тестах и на ; в 101 строке!!

```
Starting audit...
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:8:15: Unused import - org.mockito.ArgumentMatchers.anyInt. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:14:15: Unused import - org.springframework.test.web.servlet.result.MockMvcResultMatchers.content. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:16:15: Unused import - org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:38:15: Unused import - org.hamcrest.collection.IsCollectionWithSize.hasSize. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:39:15: Unused import - org.hamcrest.Matchers.everyItem. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:40:15: Unused import - org.hamcrest.Matchers.greaterThanOrEqualTo. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:41:15: Unused import - org.hamcrest.Matchers.lessThanOrEqualTo. [UnusedImports]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:101:13: '}' at column 13 should be alone on a line. [RightCurly]
[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:101:14: Empty statement. [EmptyStatement]
```
___

**Бины и область видимости**

WelcomeController.java - написано  `@GetMapping(path = "")` но не в // BEGIN //END а по заданию надо отвечать на /welcome !!! И непонятно можно ли исправлять то что не в // BEGIN //END !

 Тоже самое, убрать все про запуск приложения  и так же не может завершится, Линтер ругается на тесты

___

**Интеграционные тесты**

```
status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.
```
Тоже самое, убрать все про запуск приложения и так же не может завершится
   
Нигде не написано что надо вписать в application.yml

Не смог пройти упражнение из-за "status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов."

___

**Шаблон проектирования DTO**

а задании написано `src/main/java/io/hexlet/controller/PostController.java` а в файлах `PostSController.java` ошибка!
    
Но мы же не можем запустить из упражнения, потому проверить это нет возможности. Это стоит убрать.

___

**Преобразование DTO в сущность**

`Запуск приложения` - стоит убрать тк нет запуска!

___

**Преобразование DTO в сущность для обновления**

Линтер ругается на тест
`[ERROR] /usr/src/app/src/test/java/io/hexlet/ApplicationTest.java:26:8: Unused import - io.hexlet.dto.ProductDTO. [UnusedImports]`

___

**Автоматическая конвертация сущностей в DTO и обратно**

В приммере не плохо бы дать обяснения что и зачем тут написано, что все это значит
```
@Mapper(
    nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE,
    componentModel = MappingConstants.ComponentModel.SPRING,
    unmappedTargetPolicy = ReportingPolicy.IGNORE
)
```

если убрать `-o -q \` в Makefile то проверка проходит, иначе говорит что `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`
```
test:
	@$(MVN) test -o -q \
```

___
   
**Частичное обновление**
   
дан пример конфигурации... что это значит? как и что настраивает чего?
```
@Configuration
public class JacksonConfig {
    @Bean
    Jackson2ObjectMapperBuilder objectMapperBuilder() {
        var builder = new Jackson2ObjectMapperBuilder();
        builder.serializationInclusion(JsonInclude.Include.NON_NULL)
                .modulesToInstall(new JsonNullableModule());
        return builder;
    }
}
```

после первой проуерки `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`
если убрать `-o -q \` в Makefile, запустить, все успешно проходит, потом вернуть `-o -q \` то потом тожe все успешно проходит.
   
___

**Связь «Один ко многим»***

Проверка не проходит, `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`
если убрать `-o -q \` в Makefile, запустить, все успешно проходит, потом вернуть `-o -q \` то потом тожe все успешно проходит.

<details><summary>Вот лог когда тест не прошел </summary>

Testing
make: Entering directory '/usr/src/app'
09:17:25.744 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.test.context.support.AnnotationConfigContextLoaderUtils -- Could not detect default configuration classes for test class [io.hexlet.controller.UserControllerTest]: UserControllerTest does not declare any static, non-private, non-final, nested classes annotated with @Configuration.
09:17:25.966 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.test.context.SpringBootTestContextBootstrapper -- Found @SpringBootConfiguration io.hexlet.Application for test class io.hexlet.controller.UserControllerTest
09:17:26.062 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.devtools.restart.RestartApplicationListener -- Restart disabled due to context in which it is running
2025-09-01T09:17:26.580Z  INFO 406 --- [Launcher.main()] i.hexlet.controller.UserControllerTest   : Starting UserControllerTest using Java 21.0.4 with PID 406 (started by tirion in /usr/src/app)
2025-09-01T09:17:26.582Z  INFO 406 --- [Launcher.main()] i.hexlet.controller.UserControllerTest   : The following 1 profile is active: "test"
Mockito is currently self-attaching to enable the inline-mock-maker. This will no longer work in future releases of the JDK. Please add Mockito as an agent to your build what is described in Mockito's documentation: https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#0.3
WARNING: A Java agent has been loaded dynamically (/maven/.m2/repository/net/bytebuddy/byte-buddy-agent/1.15.11/byte-buddy-agent-1.15.11.jar)
WARNING: If a serviceability tool is in use, please run with -XX:+EnableDynamicAgentLoading to hide this warning
WARNING: If a serviceability tool is not in use, please run with -Djdk.instrument.traceUsage for more information
WARNING: Dynamic loading of agents will be disallowed by default in a future release
make: *** [Makefile:13: test] Terminated
2025-09-01T09:17:27.952Z  WARN 406 --- [Launcher.main()] o.s.w.c.s.GenericWebApplicationContext   : Exception encountered during context initialization - cancelling refresh attempt: java.lang.IllegalStateException: Could not initialize plugin: interface org.mockito.plugins.MockMaker (alternate: null)

</details>  

<details><summary>А это лог когда тест прошел со второго раза </summary>

Testing
make: Entering directory '/usr/src/app'
09:25:24.894 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.test.context.support.AnnotationConfigContextLoaderUtils -- Could not detect default configuration classes for test class [io.hexlet.controller.UserControllerTest]: UserControllerTest does not declare any static, non-private, non-final, nested classes annotated with @Configuration.
09:25:25.091 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.test.context.SpringBootTestContextBootstrapper -- Found @SpringBootConfiguration io.hexlet.Application for test class io.hexlet.controller.UserControllerTest
09:25:25.225 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.devtools.restart.RestartApplicationListener -- Restart disabled due to context in which it is running
2025-09-01T09:25:25.823Z  INFO 739 --- [Launcher.main()] i.hexlet.controller.UserControllerTest   : Starting UserControllerTest using Java 21.0.4 with PID 739 (started by tirion in /usr/src/app)
2025-09-01T09:25:25.825Z  INFO 739 --- [Launcher.main()] i.hexlet.controller.UserControllerTest   : The following 1 profile is active: "test"
Mockito is currently self-attaching to enable the inline-mock-maker. This will no longer work in future releases of the JDK. Please add Mockito as an agent to your build what is described in Mockito's documentation: https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#0.3
WARNING: A Java agent has been loaded dynamically (/maven/.m2/repository/net/bytebuddy/byte-buddy-agent/1.15.11/byte-buddy-agent-1.15.11.jar)
WARNING: If a serviceability tool is in use, please run with -XX:+EnableDynamicAgentLoading to hide this warning
WARNING: If a serviceability tool is not in use, please run with -Djdk.instrument.traceUsage for more information
WARNING: Dynamic loading of agents will be disallowed by default in a future release
OpenJDK 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended
2025-09-01T09:25:28.117Z  INFO 739 --- [Launcher.main()] i.hexlet.controller.UserControllerTest   : Started UserControllerTest in 2.897 seconds (process running for 7.289)
2025-09-01T09:25:28.189Z  INFO 739 --- [Launcher.main()] o.s.b.t.m.w.SpringBootMockServletContext : Initializing Spring TestDispatcherServlet ''
2025-09-01T09:25:28.189Z  INFO 739 --- [Launcher.main()] o.s.t.web.servlet.TestDispatcherServlet  : Initializing Servlet ''
2025-09-01T09:25:28.327Z  INFO 739 --- [Launcher.main()] o.s.t.web.servlet.TestDispatcherServlet  : Completed initialization in 136 ms
2025-09-01T09:25:28.791Z  INFO 739 --- [Launcher.main()] t.c.s.AnnotationConfigContextLoaderUtils : Could not detect default configuration classes for test class [io.hexlet.controller.TaskControllerTest]: TaskControllerTest does not declare any static, non-private, non-final, nested classes annotated with @Configuration.
2025-09-01T09:25:28.804Z  INFO 739 --- [Launcher.main()] .b.t.c.SpringBootTestContextBootstrapper : Found @SpringBootConfiguration io.hexlet.Application for test class io.hexlet.controller.TaskControllerTest
2025-09-01T09:25:28.809Z  INFO 739 --- [Launcher.main()] o.s.b.d.r.RestartApplicationListener     : Restart disabled due to context in which it is running
2025-09-01T09:25:28.852Z  INFO 739 --- [Launcher.main()] i.hexlet.controller.TaskControllerTest   : Starting TaskControllerTest using Java 21.0.4 with PID 739 (started by tirion in /usr/src/app)
2025-09-01T09:25:28.852Z  INFO 739 --- [Launcher.main()] i.hexlet.controller.TaskControllerTest   : The following 1 profile is active: "test"
2025-09-01T09:25:29.230Z  INFO 739 --- [Launcher.main()] i.hexlet.controller.TaskControllerTest   : Started TaskControllerTest in 0.421 seconds (process running for 8.402)
2025-09-01T09:25:29.241Z  INFO 739 --- [Launcher.main()] o.s.b.t.m.w.SpringBootMockServletContext : Initializing Spring TestDispatcherServlet ''
2025-09-01T09:25:29.241Z  INFO 739 --- [Launcher.main()] o.s.t.web.servlet.TestDispatcherServlet  : Initializing Servlet ''
2025-09-01T09:25:29.254Z  INFO 739 --- [Launcher.main()] o.s.t.web.servlet.TestDispatcherServlet  : Completed initialization in 13 ms

Thanks for using JUnit! Support its development at https://junit.org/sponsoring

╷
├─ JUnit Jupiter ✔
│  ├─ UserControllerTest ✔
│  │  ├─ testIndex() ✔
│  │  ├─ testShow() ✔
│  │  └─ testCreate() ✔
│  └─ TaskControllerTest ✔
│     ├─ testIndex() ✔
│     ├─ testShow() ✔
│     ├─ testDestroy() ✔
│     ├─ testCreate() ✔
│     └─ testUpdate() ✔
├─ JUnit Vintage ✔
└─ JUnit Platform Suite ✔

Test run finished after 4842 ms
[         5 containers found      ]
[         0 containers skipped    ]
[         5 containers started    ]
[         0 containers aborted    ]
[         5 containers successful ]
[         0 containers failed     ]
[         8 tests found           ]
[         0 tests skipped         ]
[         8 tests started         ]
[         0 tests aborted         ]
[         8 tests successful      ]
[         0 tests failed          ]

make: Leaving directory '/usr/src/app'

</details>  
___

**CRUD на Spring Boot**

```
Чтобы этот код заработал, необходимо внедрить общий базовый интерфейс для всех моделей, на который маппер мог бы ориентироваться и понимать, применять метод toEntity(). Мы назовем его BaseEntity:
```

Очень поверхностно написано,  зачем надо BaseEntity? как работает этот мэппер? хотелось бы подробне


Проверка не проходит, похоже что программа не запускается вообще
<details><summary>Не запускается, не проходит.  8080 порт не отвечает </summary>
Testing
make: Entering directory '/usr/src/app'
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
curl: (7) Failed to connect to localhost port 8080 after 0 ms: Couldn't connect to server
Warning: Problem : connection refused. Will retry in 5 seconds. 10 retries 
Warning: left.

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
curl: (7) Failed to connect to localhost port 8080 after 0 ms: Couldn't connect to server
Warning: Problem : connection refused. Will retry in 5 seconds. 9 retries left.
make: *** [Makefile:13: test] Terminated
</details>  

___

**Поиск**

Самый непонятный урок

___

**Слой сервисов**

В задании ничего не сказано что делать с  AuthorController.java но он открыт и там есть //BEGIN //END

написано `CRUD уже реализован` - но он НЕ реализован!!!

Тесты контроллеров закоменчены все!

Стоит убрать контроллер если в нем не надо делать правки

___


**Аутентификация**

все примеры даны по типу "делай так, просто скопируй себе в проект" а как это рабтает - не написано, какме параметры за что отвечают?
```
    public String generateToken(String username) {
        Instant now = Instant.now();
        JwtClaimsSet claims = JwtClaimsSet.builder()
                .issuer("self")
                .issuedAt(now)
                .expiresAt(now.plus(1, ChronoUnit.HOURS))
                .subject(username)
                .build();
        return this.encoder.encode(JwtEncoderParameters.from(claims)).getTokenValue();
    }
```
И это во всех примерах, "просто скопируйте себе..." хотелось бы объясниня почему именно так надо и такие настройки.

...нужны RSA-ключи... азчем они нужны? а можно без них? почти все в этой стате описано поверхнастно без обьяснений.
Для проекта - можно просто скопировать и оно как то будет работать, а как - неизвестно.

И опять сервер не отвечает по 8080. Не запускается программа?

<details><summary>Лог. Не запускается, не проходит.  8080 порт не отвечает </summary>
Testing
make: Entering directory '/usr/src/app'
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
curl: (7) Failed to connect to localhost port 8080 after 0 ms: Couldn't connect to server
Warning: Problem : connection refused. Will retry in 5 seconds. 10 retries 
Warning: left.

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
curl: (7) Failed to connect to localhost port 8080 after 0 ms: Couldn't connect to server
Warning: Problem : connection refused. Will retry in 5 seconds. 9 retries left.
make: *** [Makefile:13: test] Terminated

</details>

___

**Профили**

`Проверьте работу приложения в обоих профилях. Проверьте, что в профиле stage создается файл базы данных, и данные не удаляются после перезапуска приложения.`
никак не проверить из упражнение что там создается!

___

**Работа со статическими ресурсами**

`Запустите приложение и проверьте, что добавленные страницы отрисовываются.` - нет возможности запустить приложение в тренажере!

