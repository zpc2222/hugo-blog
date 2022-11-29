---
title: "Es入门-easy-es"
date: 2022-11-29T00:00:00+08:00
lastmod: 2022-11-29T00:00:00+08:00
author: ["藏锋"]
keywords: 
- IO
- 网络编程
categories: 
- tech
tags: 
- ElasticSearch
- Java
description: "ElasticSearch入门使用"
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


## 简介

官网地址：> https://www.easy-es.cn

选择理由：省心，省力，省代码。简单好用，少操心

## 引用
### pom依赖

``` xml
<dependency>  
    <groupId>cn.easy-es</groupId>  
    <artifactId>easy-es-boot-starter</artifactId>  
    <version>1.1.0</version>  
</dependency>  
<!--ES的相关依赖版本需要统一下,建议与服务端版本统一-->  
<dependency>  
    <groupId>org.elasticsearch.client</groupId>  
    <artifactId>elasticsearch-rest-high-level-client</artifactId>  
    <version>7.14.0</version>  
</dependency>  
<dependency>  
    <groupId>org.elasticsearch</groupId>  
    <artifactId>elasticsearch</artifactId>  
    <version>7.14.0</version>  
</dependency>
```
### 配置

如果是properties，也差距不大

```yml
easy-es:  
  # 是否开启EE自动配置,若为false时,则认为不启用本框架 
  enable: true  
  # ES连接地址+端口  
  address:  127.0.0.1:9200  
  # 关闭自带banner  
  banner: false  
  schema: http  
  username: elastic   #有设置才填写,非必须
  password: elastic
```
**拓展配置**

可缺省,不影响项目启动,为了提高生产环境性能,建议按需配置

```yml
easy-es:
  keep-alive-millis: 18000 # 心跳策略时间 单位:ms
  connectTimeout: 5000 # 连接超时时间 单位:ms
  socketTimeout: 5000 # 通信超时时间 单位:ms 
  requestTimeout: 5000 # 请求超时时间 单位:ms
  connectionRequestTimeout: 5000 # 连接请求超时时间 单位:ms
  maxConnTotal: 100 # 最大连接数 单位:个
  maxConnPerRoute: 100 # 最大连接路由数 单位:个

```

全局配置：

```yml
easy-es:
  banner: false # 默认为true 打印banner 若您不期望打印banner,可配置为false
  global-config:
    process_index_mode: smoothly #索引处理模式,smoothly:平滑模式,默认开启此模式, not_smoothly:非平滑模式, manual:手动模式
    print-dsl: true # 开启控制台打印通过本框架生成的DSL语句,默认为开启,测试稳定后的生产环境建议关闭,以提升少量性能
    distributed: false # 当前项目是否分布式项目,默认为true,在非手动托管索引模式下,若为分布式项目则会获取分布式锁,非分布式项目只需synchronized锁.
    asyncProcessIndexBlocking: true # 异步处理索引是否阻塞主线程 默认阻塞 数据量过大时调整为非阻塞异步进行 项目启动更快
    activeReleaseIndexMaxRetry: 60 # 分布式环境下,平滑模式,当前客户端激活最新索引最大重试次数若数据量过大,重建索引数据迁移时间超过60*(180/60)=180分钟时,可调大此参数值,此参数值决定最大重试次数,超出此次数后仍未成功,则终止重试并记录异常日志
    activeReleaseIndexFixedDelay: 180 # 分布式环境下,平滑模式,当前客户端激活最新索引最大重试次数 若数据量过大,重建索引数据迁移时间超过60*(180/60)=180分钟时,可调大此参数值 此参数值决定多久重试一次 单位:秒
    
    db-config:
      map-underscore-to-camel-case: false # 是否开启下划线转驼峰 默认为false
      table-prefix: daily_ # 索引前缀,可用于区分环境  默认为空 用法和MP一样
      id-type: customize # id生成策略 customize为自定义,id值由用户生成,比如取MySQL中的数据id,如缺省此项配置,则id默认策略为es自动生成
      field-strategy: not_empty # 字段更新策略 默认为not_null
      enable-track-total-hits: true # 默认开启,查询若指定了size超过1w条时也会自动开启,开启后查询所有匹配数据,若不开启,会导致无法获取数据总条数,其它功能不受影响.
      refresh-policy: immediate # 数据刷新策略,默认为不刷新
      enable-must2-filter: false # 是否全局开启must查询类型转换为filter查询类型 默认为false不转换

```

## 代码

### 数据模型（相当于MP的entity）

```java
  
/**  
 * es 数据模型  
 * <p>  
 **/  
@Data  
@Accessors(chain = true)  
@IndexName(value = "es_document", shardsNum = 3, replicasNum = 2, keepGlobalPrefix = true, maxResultWindow = 100)  
public class Document {  
    /**  
     * es中的唯一id,如果你想自定义es中的id为你提供的id,比如MySQL中的id,请将注解中的type指定为customize或直接在全局配置文件中指定,如此id便支持任意数据类型)  
     */    @IndexId(type = IdType.CUSTOMIZE)  
    private String id;  
    /**  
     * 文档标题,不指定类型默认被创建为keyword类型,可进行精确查询  
     */  
    private String title;  
    /**  
     * 文档内容,指定了类型及存储/查询分词器  
     */  
    @HighLight(mappingField = "highlightContent")  
    @IndexField(fieldType = FieldType.TEXT, analyzer = Analyzer.IK_SMART)  
    private String content;  
    /**  
     * 作者 加@TableField注解,并指明strategy = FieldStrategy.NOT_EMPTY 表示更新的时候的策略为 创建者不为空字符串时才更新  
     */  
    @IndexField(strategy = FieldStrategy.NOT_EMPTY)  
    private String creator;  
    /**  
     * 创建时间  
     */  
    @IndexField(fieldType = FieldType.DATE, dateFormat = "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis")  
    private String gmtCreate;  
    /**  
     * es中实际不存在的字段,但模型中加了,为了不和es映射,可以在此类型字段上加上 注解@TableField,并指明exist=false  
     */    @IndexField(exist = false)  
    private String notExistsField;  
    /**  
     * 地理位置经纬度坐标 例如: "40.13933715136454,116.63441990026217"  
     */    @IndexField(fieldType = FieldType.GEO_POINT)  
    private String location;  
    /**  
     * 图形(例如圆心,矩形)  
     */    @IndexField(fieldType = FieldType.GEO_SHAPE)  
    private String geoLocation;  
    /**  
     * 自定义字段名称  
     */  
    @IndexField(value = "wu-la", fieldType = FieldType.TEXT, analyzer = Analyzer.IK_SMART, searchAnalyzer = Analyzer.IK_SMART, fieldData = true)  
    private String customField;  
  
    /**  
     * 高亮返回值被映射的字段  
     */  
    private String highlightContent;  
    /**  
     * 文档点赞数  
     */  
    private Integer starNum;  
}
```

###  mapper

```java
/**  
 * mapper 相当于Mybatis-plus的mapper  
 * <p>  
 **/  
public interface DocumentMapper extends BaseEsMapper<Document> {  
}
```

### controller 调用接口

```
@RestController  
@RequestMapping("/es")  
@Tag(name = "es案例")  
@RequiredArgsConstructor  
@Slf4j  
public class EsController {  
  
    private final DocumentMapper documentMapper;  
  
    /**  
     * 初始化插入数据,默认开启自动挡,自动挡模式下,索引会自动创建及更新. 若未开启自动挡,则在此步骤前需先调用创建索引API完成索引创建  
     *  
     * @return  
     */  
    @Operation(summary = "添加文档")  
    @PostMapping("insert")  
    public Integer insert(@RequestBody Document document) {  
        document.setGmtCreate(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));  
        return documentMapper.insert(document);  
    }  
  
    @Operation(summary = "批量添加测试")  
    @PostMapping("batchInsert")  
    public void testBatchInsert() {  
        List<Document> documentList = new ArrayList<>();  
        for (int i = 2; i < 23; i++) {  
            Document document = new Document();  
            document.setId(Integer.toString(i));  
            document.setTitle("测试文档" + i);  
            document.setContent("测试内容" + i);  
            document.setCreator("老汉" + i);  
            document.setGmtCreate(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));  
            document.setCustomField("自定义字段" + i);  
            Point point = new Point(13.400544 + i, 52.530286 + i);  
            document.setGeoLocation(point.toString());  
            document.setStarNum(i);  
            if (i == 2) {  
                document.setLocation("40.17836693398477,116.64002551005981");  
                document.setStarNum(1);  
            } else if (i == 3) {  
                document.setLocation("40.19103839805197,116.5624013764374");  
            } else if (i == 4) {  
                document.setLocation("40.13933715136454,116.63441990026217");  
            }  
            documentList.add(document);  
        }  
        int count = documentMapper.insertBatch(documentList);  
        Assert.equals(documentList.size(), count);  
    }  
  
    /**  
     * 演示根据标题精确查询文章  
     * 例如title传值为 我真帅,那么在当前配置的索引下,所有标题为'我真帅'的文章都会被查询出来  
     * 其它各种场景的查询使用,请移步至test模块  
     *  
     * @param title  
     * @return  
     */  
    @Operation(summary = "根据标题精确查询文章")  
    @GetMapping("/listDocumentByTitle")  
    public List<Document> listDocumentByTitle(@RequestParam String title) {  
        // 实际开发中会把这些逻辑写进service层 这里为了演示方便就不创建service层了  
        LambdaEsQueryWrapper<Document> wrapper = new LambdaEsQueryWrapper<>();  
        wrapper.eq(Document::getTitle, title);  
        return documentMapper.selectList(wrapper);  
    }  
  
    @Operation(summary = "查询id是1,2,3的")  
    @GetMapping("/queryConditionIn")  
    public List<Document> testConditionIn() {  
        LambdaEsQueryWrapper<Document> wrapper = new LambdaEsQueryWrapper<>();  
        wrapper.in(Document::getId, "1", "2", "3");  
        List<Document> documents = documentMapper.selectList(wrapper);  
        return documents;  
    }  
  
    /**  
     * 演示根据title删除文章，同时会被 DeleteInterceptor 拦截，执行逻辑删除  
     *  
     * @param title  
     * @return  
     */  
    @Operation(summary = "根据title删除文章")  
    @GetMapping("/deleteDocumentByTitle")  
    public Integer deleteDocumentByTitle(@RequestParam String title) {  
        // 实际开发中会把这些逻辑写进service层 这里为了演示方便就不创建service层了  
        LambdaEsQueryWrapper<Document> wrapper = new LambdaEsQueryWrapper<>();  
        wrapper.eq(Document::getTitle, title);  
        return documentMapper.delete(wrapper);  
    }  
  
    /**  
     * 自定义注解指定高亮返回字段,高亮查询测试  
     *  
     * @param content  
     * @return  
     */  
    @Operation(summary = "高亮查询测试")  
    @GetMapping("/highlightSearch")  
    public List<Document> highlightSearch(@RequestParam String content) {  
        // 实际开发中会把这些逻辑写进service层 这里为了演示方便就不创建service层了  
        LambdaEsQueryWrapper<Document> wrapper = new LambdaEsQueryWrapper<>();  
        wrapper.match(Document::getContent, content);  
        return documentMapper.selectList(wrapper);  
    }
```

### 注解

1. 启动类添加
```
启动类添加@EsMapperScan("com.xxx.mapper")
```
mapper扫描注解,功能与MP的@MapperScan一致

2. @IndexName  索引名注解，标识实体类对应的索引 对应MP的@TableName注解

3. @IndexId  ES主键注解

4. @IndexField  ES字段注解

启动成功，打印：Congratulations auto process index by Easy-Es is don

具体使用，可以参考代码里面的test工程，工程名：easy-es-test  路径：cn.easyes.test