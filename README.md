# Fixing-Spring-Boot-lessons
some notations for Spring Boot lessons

**1) Знакомство с Spring Boot**
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
**2) Аннотации**
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
**3) HTTP и CRUD приложения**
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






