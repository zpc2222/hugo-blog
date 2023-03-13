---
title: "MybatisPlus常用"
date: 2023-03-13T00:00:00+08:00
lastmod: 2023-03-13T00:00:00+08:00
author: ["藏锋"]
keywords: 
- mybatis
categories: 
- tech
tags: 
- MybatisPlus
- Mybatis
description: "MybatisPlus常用一些语法及心得"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

# 什么是MybatisPlus
	MybatisPlus可以节省大量时间，所有的CRUD代码都可以自动化完成
	MyBatis-Plus是一个MyBatis的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。
	官网：https://baomidou.com/

# 使用

## 配置

```yml
server:  
  port: 8082  
  servlet:  
    context-path: /oc-app  
spring:  
  jackson:  
    time-zone: GMT+8  
  datasource:  
    dynamic:  
      primary: db1  
      datasource:  
        db1:  
          # 数据库的JDBC链接  
          url: jdbc:mysql://127.0.0.1:3306/test?createDatabaseIfNotExist=true&useUnicode=true&characterEncoding=utf8&useSSL=false&useLegacyDatetimeCode=false&&serverTimezone=Asia/Shanghai  
          # 数据库用户名  
          username: root  
          # 数据库密码  
          password: 123456  
        db2:  
          # 数据库的JDBC链接  
          url: jdbc:mysql://127.0.0.1:3306/admin?createDatabaseIfNotExist=true&useUnicode=true&characterEncoding=utf8&useSSL=false&useLegacyDatetimeCode=false&&serverTimezone=Asia/Shanghai  
          # 数据库用户名  
          username: root  
          # 数据库密码  
          password: 654321  
  flyway: # 是否启用flyway  
    enabled: true  
    # 编码格式，默认UTF-8  
    encoding: UTF-8  
    # 迁移sql脚本文件存放路径，默认db/migration  
    locations: classpath:db/migration  
    # 迁移sql脚本文件名称的前缀，默认V  
    sql-migration-prefix: V  
    # 迁移sql脚本文件名称的分隔符，默认2个下划线__  
    sql-migration-separator: __  
    # 迁移sql脚本文件名称的后缀  
    sql-migration-suffixes: .sql  
    # 迁移时是否进行校验，默认true  
    validate-on-migrate: true  
    # 当迁移发现数据库非空且存在没有元数据的表时，自动执行基准迁移，新建schema_version表  
    baseline-on-migrate: true  
  redis:  
    host: 192.168.1.1 
    port: 6379  
    password: admin@123!  
  cache:  
    type: REDIS  
    redis:  
      time-to-live: 120  
  mvc:  
    pathmatch:  
      matching-strategy: ant_path_matcher  
  
  main:  
    allow-bean-definition-overriding: true  
    # mybatis plus 配置  
mybatis-plus:  
  configuration: # 下划线转驼峰  
    map-underscore-to-camel-case: true  
    #日志打印  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
    # 扫描 xml 文件位置  
  mapper-locations: classpath:mapper/*Mapper.xml  
  global-config:  
    db-config: # 使用雪花算法主键填充策略  
      id-type: assign_id
```

在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹：
```java
@SpringBootApplication
@MapperScan("com.xx.mapper")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

## 实现
实体类
```java
@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```
编写 Mapper 包下的 `UserMapper`接口
```java
public interface UserMapper extends BaseMapper<User> {

}
```

service层及实现类
```java
public interface UserService extends IService<User> {  
}

@Service  
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {  
  
}
```

## 注解

@TableId(value = "user_id", type = IdType.ASSIGN_ID)  
private Long userId;
分配 ID(主键类型为 Number(Long 和 Integer)或 String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法)
如上使用的id为雪花算法生成的，比较长，建议使用自增AUTO，数据库不进行物理删除，而是逻辑删除

## 表达式与sql对比

```text
eq == equal  等于
ne == not equal 不等于
gt == greater than 大于
lt == less than 小于
ge ==  greater than or equal 大于等于
le == less than or equal 小于等于
in == in 包含（数组）
isNull == 等于null
isNotNull == 不等于null
orderByDesc == 倒序排序
orderByAsc == 升序排序
or == 或者
and == 并且
between == 在2个条件之间(包括边界值)
like == 模糊查询
clear == 清除
apply == 拼接sql
lambda == 使用lambda表达式
exists == 临时表
```

## 常用注解

实体类中创建时间以及修改时间可以加入
```java
/**  
 * 创建时间  
 */  
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")  
@TableField(fill = FieldFill.INSERT)  
private LocalDateTime createTime;  
  
/**  
 * 更新时间  
 */  
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")  
@TableField(fill = FieldFill.INSERT_UPDATE)  
private LocalDateTime updateTime;
```

编写处理器来处理这个注释就会在写操作时候自动生成当前时间
```java
/**  
 * 实现自动化操作创建时间和修改时间  
 */  
@Component  
public class DateTimeFillConfig implements MetaObjectHandler {  
    @Override  
    public void insertFill(MetaObject metaObject) {  
        //设置属性值  
        this.setFieldValByName("createdTime", LocalDateTime.now(),metaObject);  
        this.setFieldValByName("updatedTime",LocalDateTime.now(),metaObject);  
    }  
  
    @Override  
    public void updateFill(MetaObject metaObject) {  
        this.setFieldValByName("updatedTime",LocalDateTime.now(),metaObject);  
    }  
}
```

## 复杂查询案例

```java
        //封装查询条件
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
		//模糊查询
		queryWrapper.like("a.nick_name", vo.getName());
		// 手机号/用户名称查询,当用户名为一串数字时默认使用手机号查询
        if (StringUtils.isNotBlank(vo.getNameOrMobile())) {
            if (NumberUtils.isDigits(vo.getNameOrMobile())) {
                queryWrapper.and(wq->wq.like("b.mobile", vo.getNameOrMobile())
                        .or().like("b.nick_name",vo.getNameOrMobile()));
            } else {
                queryWrapper.like("b.nick_name", vo.getNameOrMobile());
            }
        }
		// 时间范围查询
        if (StringUtils.isNotBlank(vo.getStartTime())) {
            queryWrapper.ge("a.created_time", vo.getStartTime());
        }
        if (StringUtils.isNotBlank(vo.getEndTime())) {
            queryWrapper.le("a.created_time", vo.getEndTime());
        } 
		// 精准查询
        if (StringUtils.isNotBlank(vo.getId())) {
            queryWrapper.eq("a.id", vo.getId());
        }
		//状态不在其中的
        if (StringUtils.isBlank(vo.getStatus()) && "2".equals(vo.getType())) {
            queryWrapper.notIn("a.status", List.of(1, 2, 3));
        }
		queryWrapper.notIn("a.status", List.of(1,2,3));
		// 时间进行排序
        // where语句后面不能直接增加排序，导致sql解析异常
        if (!queryWrapper.isEmptyOfWhere()) {
            queryWrapper.orderByDesc("a.created_time");
        }
		
		IPage<User> pages = getBaseMapper().getPageList(pager, queryWrapper);
```