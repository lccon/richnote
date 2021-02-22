#### 1、Spring1.4之后版本获取配置文件内容添加到属性中方式

```java
@Component  //交给spring进行管理，通过@Autowired注入获取
@ConfigurationProperties(prefix = "web")  //将配置文件以web开头的注入属性中
@PropertySource(value = {"classpath:/web-file.properties", "classpath:/web-content.properties"})  //拿取所配置的文件内容
public class User {
}
```

 #### 2、获取配置文件属性值

```java
import org.springframework.core.env.Environment;

@Autowired
private Environment env;

env.getProperty("server.port"); //获取配置文件内容
```

#### 3、定时器

```java
@SpringBootApplication
@EnableScheduling       //主函数添加定时器使用配置
public class SpringBootStudy11Application {
	public static void main(String[] args) {
		SpringApplication.run(SpringBootStudy11Application.class, args);
	}
}
```

```java
@Component  //交给spring来管理
public class TestController {

    SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");

    // 每三秒执行一次
    @Scheduled(fixedDelay = 3000)
    public void test() {
        System.out.println(sdf.format(new Date()));
    }

    //第一次延迟1秒执行，当执行完后3秒再执行
    @Scheduled(initialDelay = 2000, fixedDelay = 3000)
    public void timerInit() {
        System.out.println("init : " + sdf.format(new Date()));
    }

    // 每天的11:10执行
    @Scheduled(cron = "00 10 11 * * ?")
    public void test2() {
        System.out.println("current time" + sdf.format(new Date()));
    }
}
```

