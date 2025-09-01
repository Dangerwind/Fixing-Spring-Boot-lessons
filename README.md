мои сохраненные решения: https://ru.hexlet.io/code_reviews?q%5Buser_id_eq%5D=873990

Может быть я сильно не прав в чем-то, или я что то не так делаю.... но:

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



___

**Обработка ошибок**
   
В упражнении файл GlobalExceptionHandler.java

там где все import хорошо бы добавить // BEGIN //END чтобы было понятно что туда надо добавить импорт нашего исключения `import io.hexlet.exception.ResourceNotFoundException;`
а то не сразу понятно что он не добавлен и его надо отдельно прописать.

___

**Сравнение сущностей**


___
 
**Аудит в JPA**

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

Получается что задание пройти невозможно. После нескольких запусков тесты прошли!


<details><summary>Первый запусе. вот Лог который "Упс, что то пошло не так, код работал слишком долго и был остановлен"</summary>
Testing
make: Entering directory '/usr/src/app'
12:49:12.579 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.test.context.support.AnnotationConfigContextLoaderUtils -- Could not detect default configuration classes for test class [io.hexlet.ApplicationTest]: ApplicationTest does not declare any static, non-private, non-final, nested classes annotated with @Configuration.
12:49:12.751 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.test.context.SpringBootTestContextBootstrapper -- Found @SpringBootConfiguration io.hexlet.Application for test class io.hexlet.ApplicationTest
12:49:12.892 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.devtools.restart.RestartApplicationListener -- Restart disabled due to context in which it is running

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.4.5)

2025-09-01T12:49:13.545Z  INFO 152 --- [Launcher.main()] io.hexlet.ApplicationTest                : Starting ApplicationTest using Java 21.0.4 with PID 152 (started by tirion in /usr/src/app)
2025-09-01T12:49:13.547Z  INFO 152 --- [Launcher.main()] io.hexlet.ApplicationTest                : No active profile set, falling back to 1 default profile: "default"
2025-09-01T12:49:14.035Z  INFO 152 --- [Launcher.main()] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2025-09-01T12:49:14.110Z  INFO 152 --- [Launcher.main()] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 64 ms. Found 1 JPA repository interface.
2025-09-01T12:49:14.210Z  INFO 152 --- [Launcher.main()] beddedDataSourceBeanFactoryPostProcessor : Replacing 'dataSource' DataSource bean with embedded version
2025-09-01T12:49:14.366Z  INFO 152 --- [Launcher.main()] o.s.j.d.e.EmbeddedDatabaseFactory        : Starting embedded database: url='jdbc:h2:mem:b66f7e23-aa2c-4719-b965-796a6ad01367;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=false', username='sa'
2025-09-01T12:49:14.811Z  INFO 152 --- [Launcher.main()] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2025-09-01T12:49:14.910Z  INFO 152 --- [Launcher.main()] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 6.6.13.Final
2025-09-01T12:49:15.117Z  INFO 152 --- [Launcher.main()] o.h.c.internal.RegionFactoryInitiator    : HHH000026: Second-level cache disabled
2025-09-01T12:49:15.507Z  INFO 152 --- [Launcher.main()] o.s.o.j.p.SpringPersistenceUnitInfo      : No LoadTimeWeaver setup: ignoring JPA class transformer
2025-09-01T12:49:15.620Z  INFO 152 --- [Launcher.main()] org.hibernate.orm.connections.pooling    : HHH10001005: Database info:
	Database JDBC URL [Connecting through datasource 'org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseFactory$EmbeddedDataSourceProxy@5fc811f3']
	Database driver: undefined/unknown
	Database version: 2.3.232
	Autocommit mode: undefined/unknown
	Isolation level: undefined/unknown
	Minimum pool size: undefined/unknown
	Maximum pool size: undefined/unknown
2025-09-01T12:49:16.821Z  INFO 152 --- [Launcher.main()] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000489: No JTA platform available (set 'hibernate.transaction.jta.platform' to enable JTA platform integration)
Hibernate: create table tasks (id bigint generated by default as identity, created_at date, description varchar(255), title varchar(255), updated_at date, primary key (id))
2025-09-01T12:49:16.920Z  INFO 152 --- [Launcher.main()] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
make: *** [Makefile:12: test] Terminated
2025-09-01T12:49:17.459Z  INFO 152 --- [ionShutdownHook] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
</details> 
___
    
**Возможности JPA Repository**

    
и так же не может завершится `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`
Даже если код правильно написан.
    
В задании ниже не написано что надо написать в application.yml а файл есть и там есть //BEGIN //END
   
___


**Инверсия зависимостей**

   Тоже самое, убрать все про запуск приложения  и так же не может завершится

Линтер ругается на неиспользуемые import в тестах и на `;` в 101 строке!!

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

 Линтер ругается на тесты

___

**Интеграционные тесты**

```
status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.
```

   
Нигде не написано что надо вписать в application.yml

Пройти можно только если убрать из Makefile  все `-o -q \` везде.

<details><summary>вот Лог который "Упс, что то пошло не так, код работал слишком долго и был остановлен"</summary>
Testing
make: Entering directory '/usr/src/app'
12:55:26.138 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.test.context.support.AnnotationConfigContextLoaderUtils -- Could not detect default configuration classes for test class [io.hexlet.controller.TaskControllerTest]: TaskControllerTest does not declare any static, non-private, non-final, nested classes annotated with @Configuration.
12:55:26.276 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.test.context.SpringBootTestContextBootstrapper -- Found @SpringBootConfiguration io.hexlet.Application for test class io.hexlet.controller.TaskControllerTest
12:55:26.532 [org.junit.platform.console.ConsoleLauncher.main()] INFO org.springframework.boot.devtools.restart.RestartApplicationListener -- Restart disabled due to context in which it is running

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.4.5)

2025-09-01T12:55:26.900Z  INFO 209 --- [Launcher.main()] i.hexlet.controller.TaskControllerTest   : Starting TaskControllerTest using Java 21.0.4 with PID 209 (started by tirion in /usr/src/app)
2025-09-01T12:55:26.902Z  INFO 209 --- [Launcher.main()] i.hexlet.controller.TaskControllerTest   : No active profile set, falling back to 1 default profile: "default"
2025-09-01T12:55:27.695Z  INFO 209 --- [Launcher.main()] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2025-09-01T12:55:27.751Z  INFO 209 --- [Launcher.main()] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 44 ms. Found 1 JPA repository interface.
2025-09-01T12:55:28.507Z  INFO 209 --- [Launcher.main()] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2025-09-01T12:55:28.565Z  INFO 209 --- [Launcher.main()] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 6.6.13.Final
2025-09-01T12:55:28.607Z  INFO 209 --- [Launcher.main()] o.h.c.internal.RegionFactoryInitiator    : HHH000026: Second-level cache disabled
2025-09-01T12:55:28.920Z  INFO 209 --- [Launcher.main()] o.s.o.j.p.SpringPersistenceUnitInfo      : No LoadTimeWeaver setup: ignoring JPA class transformer
2025-09-01T12:55:28.957Z  INFO 209 --- [Launcher.main()] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2025-09-01T12:55:29.175Z  INFO 209 --- [Launcher.main()] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Added connection conn0: url=jdbc:h2:mem:f4185d51-36fd-4ae4-9102-139160a80992 user=SA
2025-09-01T12:55:29.178Z  INFO 209 --- [Launcher.main()] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2025-09-01T12:55:29.237Z  INFO 209 --- [Launcher.main()] org.hibernate.orm.connections.pooling    : HHH10001005: Database info:
	Database JDBC URL [Connecting through datasource 'HikariDataSource (HikariPool-1)']
	Database driver: undefined/unknown
	Database version: 2.3.232
	Autocommit mode: undefined/unknown
	Isolation level: undefined/unknown
	Minimum pool size: undefined/unknown
	Maximum pool size: undefined/unknown
2025-09-01T12:55:30.173Z  INFO 209 --- [Launcher.main()] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000489: No JTA platform available (set 'hibernate.transaction.jta.platform' to enable JTA platform integration)
Hibernate: create table tasks (id bigint generated by default as identity, created_at date, description varchar(255), title varchar(255), updated_at date, primary key (id))
2025-09-01T12:55:30.242Z  INFO 209 --- [Launcher.main()] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2025-09-01T12:55:30.903Z  WARN 209 --- [Launcher.main()] JpaBaseConfiguration$JpaWebConfiguration : spring.jpa.open-in-view is enabled by default. Therefore, database queries may be performed during view rendering. Explicitly configure spring.jpa.open-in-view to disable this warning
2025-09-01T12:55:31.367Z  INFO 209 --- [Launcher.main()] o.s.b.t.m.w.SpringBootMockServletContext : Initializing Spring TestDispatcherServlet ''
2025-09-01T12:55:31.367Z  INFO 209 --- [Launcher.main()] o.s.t.web.servlet.TestDispatcherServlet  : Initializing Servlet ''
2025-09-01T12:55:31.370Z  INFO 209 --- [Launcher.main()] o.s.t.web.servlet.TestDispatcherServlet  : Completed initialization in 1 ms
2025-09-01T12:55:31.417Z  INFO 209 --- [Launcher.main()] i.hexlet.controller.TaskControllerTest   : Started TaskControllerTest in 4.89 seconds (process running for 9.223)
Mockito is currently self-attaching to enable the inline-mock-maker. This will no longer work in future releases of the JDK. Please add Mockito as an agent to your build what is described in Mockito's documentation: https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#0.3
WARNING: A Java agent has been loaded dynamically (/maven/.m2/repository/net/bytebuddy/byte-buddy-agent/1.15.4/byte-buddy-agent-1.15.4.jar)
WARNING: If a serviceability tool is in use, please run with -XX:+EnableDynamicAgentLoading to hide this warning
WARNING: If a serviceability tool is not in use, please run with -Djdk.instrument.traceUsage for more information
WARNING: Dynamic loading of agents will be disallowed by default in a future release
OpenJDK 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended
make: *** [Makefile:13: test] Terminated
2025-09-01T12:55:32.200Z  INFO 209 --- [ionShutdownHook] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-09-01T12:55:32.203Z  INFO 209 --- [ionShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-09-01T12:55:32.216Z  INFO 209 --- [ionShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
</details>

___

**Шаблон проектирования DTO**

а задании написано `src/main/java/io/hexlet/controller/PostController.java` а в файлах `PostSController.java` ошибка!


___

**Преобразование DTO в сущность**


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

Не очень понятный урок.
 Много примером и мало обьяснений.
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


___

**Работа со статическими ресурсами**



