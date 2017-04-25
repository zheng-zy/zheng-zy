---
title: spring-retry异常重试机制
description: spring-retry异常重试机制
tags:
  - SpringBoot
  - Java
categories: tech
abbrlink: 59773
date: 2017-04-11 11:01:46
---

### spring-retry

> 在平常我们提供远程调用API时，都会因为各种原因引起调用失败，这个时候需要提供异常重试机制，
一般我们都使用for或者while机制，现在springboot给我们提供了一个良好的方法机制。

### 代码

```
@SpringBootApplication
@EnableRetry
public class SpringRetryApplication {

    public static void main(String[] args) {
	        SpringApplication.run(SpringRetryApplication.class, args);
    }
}
@Service
public class HelloService {

    private Logger logger = LoggerFactory.getLogger(getClass());

    private Random random = new Random();

    @Retryable(include = RuntimeException.class, maxAttempts = 3, backoff = @Backoff(value = 2000L))
    public String testRetry() {
        int randomInt = random.nextInt(10);
        if (randomInt < 7) {
            logger.error("异常： {}", randomInt);
            throw new RuntimeException("call fail");
        } else {
            logger.info("成功");
            return "success";
        }
    }

    @Recover
    public String reliable() {
        return "cloud native java (reliable)";
    }

}
@RestController
public class HelloController {

    @Resource
    private HelloService helloService;

    @GetMapping("/test")
    public String test() {
        return helloService.testRetry();
    }


}
```
[测试链接访问](http://127.0.0.1:8080/test)

### @Retryable参数解析

value：指定异常重试
include：和value类似
exclude：排除某些异常

如果value，include，exclude都为空，任何异常都重试

maxAttempts：最大重试次数

backoff：重试策略，默认使用@Backoff，value为1000L，每间隔1秒重试一次，
multiplier默认值为0，表示固定暂停1秒重试，如果调整为2，那么第一次为1秒，第二次为2秒，第三次为4秒。

### 示例代码

[github下载](https://github.com/zheng-zy/learn-spring-cloud)

### 版权声明
作者：苏陌年
出处：http://www.zhengzy.top/ 
版权所有，欢迎保留原文链接进行转载：)
