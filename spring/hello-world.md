# HelloWorld

## Application.java

```java
@SpringBootApplication
public class Application{
  public static void main(String[] args){
    Application.run(Application.class, args);
  }
}
```

`@SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan`

## Controller

```java
@RestController
class HelloWorldController {

  @RequestMapping("/")
  public String hello(){
    return "- -! Hello World!";
  }
}
```

`@RestController = @Controller + @ResponseBody`

## 注解

`@Controller` 注解 Web 控制器类，将 HTTP 请求根据路径给对应的类处理

`@RestController` 默认自动返回 JSON 的 Restful 控制器

`@RequestMapping` 注解一个路由，定义在类上，相当于一个路由组。参数有路由规则和 HTTP 方法，简写方式有：`@GetMapping`, `@PutMapping`

`@PathVariable` 注解路径参数 如 /user/{id}

`@RequestParam` 注解请求参数 如 ?user=a 或 post['user']=a

`@RequestBody` 注解请求体

`@RequestHeader` 注解请求头

`@CookieValue` 注解一个 Cookie 值

`@SessionAttribute`注解一个 Session 值