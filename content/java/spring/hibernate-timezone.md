---
title: 'Hibernate時區設定'
date: 2019-05-24
weight: 50
tags: ["spring","hibernate","time"]
---





需要應付不同時區時，資料庫儲存UTC+0的時間，頁面動態決定要顯示的時區。但從資料庫取出的`java.util.Date`會自動設定為環境預設時區

##### 方案1

修改JVM預設時區：

```bash
java -Duser.timezone=UTC -jar project.jar
```

或是

```java
@SpringBootApplication
public class Application {
 
  @PostConstruct
  void started() {
    TimeZone.setDefault(TimeZone.getTimeZone("UTC"));
  }
 
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
 
}
```



##### 方案2

用Hibernate的話可以設定JDBC時區

```properties
spring.jpa.properties.hibernate.jdbc.time_zone = UTC
```



參考資料

<https://moelholm.com/2016/11/09/spring-boot-controlling-timezones-with-hibernate/>















