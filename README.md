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
тут @Autowired и @AllArgsConstructor делают одно и тоже, стоит `@Autowired` убрать (я понимаю что это пример, не суть, но для насмотренности лучше написать коректно)
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

похоже проблема в Makefile 

```
test:
	@$(MVN) test -o -q
```

Получается что задание пройти невозможно.
__

**Возможности JPA Repository**
    
Тоже самое, убрать все про запуск приложения.
    
и так же не может завершится `status: finished → Упс, что то пошло не так, код работал слишком долго и был остановлен. Проверьте условия выхода из циклов.`
Даже если код правильно написан.
    
В задании ниже не написано что надо написать в application.yml а файл есть и там есть //BEGIN //END
   
___
   
