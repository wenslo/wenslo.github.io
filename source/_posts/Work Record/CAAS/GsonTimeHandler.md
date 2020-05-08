---
title: gson转换LocalDateTime时碰到的问题

categories: 

- 工作日常记录

tags: 

- 工作
date: 2020-05-08 19:07:03

---

当使用gson转换为LocalDateTime的属性时，因为LocalDateTime相当于是个数组，而元数据为 yyyy-MM-dd HH:mm:ss的字符串，转换时会发生错误，需要进行如下的配置

```java
    /**
     * 默认日期时间格式
     */
    private static final String DEFAULT_DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
    /**
     * 默认日期格式
     */
    private static final String DEFAULT_DATE_FORMAT = "yyyy-MM-dd";
    /**
     * 默认时间格式
     */
    private static final String DEFAULT_TIME_FORMAT = "HH:mm:ss";

    private static final DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT);
    private static final DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT);
    private static final DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT);

    @Bean
    public Gson gson() {
        return new GsonBuilder()
                .registerTypeAdapter(LocalDateTime.class,
                        (JsonDeserializer<LocalDateTime>) (json, type, jsonDeserializationContext)
                                -> LocalDateTime.parse(json.getAsString(), dateTimeFormatter))
                .registerTypeAdapter(LocalDate.class,
                        (JsonDeserializer<LocalDate>) (json, type, jsonDeserializationContext)
                                -> LocalDate.parse(json.getAsString(), dateFormatter))
                .registerTypeAdapter(LocalTime.class,
                        (JsonDeserializer<LocalTime>) (json, type, jsonDeserializationContext)
                                -> LocalTime.parse(json.getAsString(), timeFormatter))
                .create();
    }
```



