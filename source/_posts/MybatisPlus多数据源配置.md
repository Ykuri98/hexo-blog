---
title: MybatisPlus多数据源配置
date: 2023-08-28 17:11:31
categories:
- 技术
tags:
- java
---

采用AOP思想，对DataSource注解进行动态切换，核心思想是依靠 继承AbstractRoutingDataSource，重写determineCurrentLookupKey()方法，在该方法中使用DatabaseContextHolder获取当前线程的dataSource。

首先要在配置文件中配置不同的数据源。

```yaml
spring:
  datasource:
    druid:
      dianshang:
        driver-class-name: oracle.jdbc.OracleDriver
        username: xxx
        password: xxx
        url: jdbc:oracle:thin:@//10.21.1.18:1531/oradev
      pos:
        driver-class-name: oracle.jdbc.OracleDriver
        username: xxx
        password: xxx
        url: jdbc:oracle:thin:@//10.1.8.58:1521/orcl
```

在项目中创建如下文件

![1](D:\blog\hexo\source\_posts\MybatisPlus多数据源配置\1.png)

首先，编写DynamicDataSource类集成AbstractRoutingDataSource，重写determineCurrentLookupKey方法，该方法主要作用是选择数据源的key

```java
/**
 * 动态数据源
 * */	
public class DynamicDataSource extends AbstractRoutingDataSource {

    public DynamicDataSource(DataSource defaultTargetDataSource, Map<Object, Object> targetDataSources) {
        // 设置默认的数据源
        super.setDefaultTargetDataSource(defaultTargetDataSource);
        // 设置目标数据源的集合
        super.setTargetDataSources(targetDataSources);
        // 初始化bean时，通过外界数据为bean填充属性
        super.afterPropertiesSet();
    }

    @Override
    protected Object determineCurrentLookupKey() {
        return DataSourceHolder.getDataSourceType();
    }
}

```

其次自定义@DataSource注解，在需要切换数据的Dao添加此注解

```java
/**
 * 备注：自定义数据源选择注解
 **/
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface DataSource {
    String name() default "";
}
```

配置AOP切面类

```java
@Aspect
@Component
public class DataSourceAspect {

    @Pointcut("@annotation(com.xxx.aspect.DataSource)")
    public void dataSourcePointCut() {

    }

    @Around("dataSourcePointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        MethodSignature signature = (MethodSignature) point.getSignature();
        Method method = signature.getMethod();

        DataSource dataSource = method.getAnnotation(DataSource.class);
        if(dataSource == null){
           DataSourceHolder.setDataSourceType(dataSource.name());
        }
        try {
            return point.proceed();
        } finally {
            DataSourceHolder.clearDataSource();
        }
    }
}
```

配置DataSourceHolder

```java
public class DataSourceHolder {

    /**
     * threadLocal存储数据源
     */
    private static final ThreadLocal<String> DATA_SOURCE = new ThreadLocal<>();

    /**
     * 设置当前数据源
     *
     * @param type
     */
    public static void setDataSourceType(String type) {
        DATA_SOURCE.set(type);
    }

    /**
     * 获取当前数据源
     *
     * @return
     */
    public static String getDataSourceType() {
        return DATA_SOURCE.get();
    }

    /**
     * 清除当前数据源
     */
    public static void clearDataSourceType() {
        DATA_SOURCE.remove();
    }
}
```

在项目的配置包中添加配置类

添加数据源配置DataSourceConfig

```java
@Configuration
public class DataSourceConfig {

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.druid.dianshang")
    public DruidDataSource dataSource1() {
        return DruidDataSourceBuilder.create().build();
    }

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.druid.pos")
    public DruidDataSource dataSource2() {
        return DruidDataSourceBuilder.create().build();
    }

    @Primary
    @Bean(name = "dynamicDataSource")
    public DynamicDataSource dataSource(@Qualifier("dataSource1") DataSource dataSource1,
                                        @Qualifier("dataSource2") DataSource dataSource2) {
        Map<Object, Object> targetDataSources = new HashMap<>(16);
        targetDataSources.put("dianshang", dataSource1);
        targetDataSources.put("pos", dataSource2);
        return new DynamicDataSource(dataSource1, targetDataSources);
    }
}
```

最后配置启动类

```java
// 需要去除springBoot自带的数据源配置
@SpringBootApplication(exclude= {DataSourceAutoConfiguration.class})
@MapperScan(basePackages = "com.xxx.mapper")
// 导入自己写的数据源配置类
@Import({DataSourceConfig.class})
public class StartApp {
    public static void main(String[] args) {
        SpringApplication.run(StartApp.class);
    }
}
```

如果配了mybatisPlus，需要更改其中的一些配置

```java
@Configuration
public class MybatisPlusConfig {
    /**
     * oracle序列生成器
     */
    @Bean
    public OracleKeyGenerator oracleKeyGenerator() {
        return new OracleKeyGenerator();
    }


    /**
     * oracle事务管理器
     */
    @Bean(name = "mybatisTransactionManager")
    @Primary //事务默认数据库
    // Qualifier注解需要改为dynamicDataSource
    public DataSourceTransactionManager testTransactionManager(@Qualifier("dynamicDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
}
```

之后在写Mybatis的mapper接口时用注解声明要使用的数据源就可以实现动态切换啦

```java
public interface Mapper {
    @DataSource(name = "pos")
    String querySkuIdByBarcode(@Param("barcode") String barcode);
}
```
