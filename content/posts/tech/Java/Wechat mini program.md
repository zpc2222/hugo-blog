---
title: "微信小程序相关"
date: 2022-09-26T00:17:58+08:00
lastmod: 2022-09-29T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- java
- 小程序
description: "微信小程序相关"
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

 微信小程序相关
[小程序地址](https://mp.weixin.qq.com/wxamp/index/index?lang=zh_CN&token=176606198)

## 1.登陆
获取openid方法，通过页面获取的jsCode，加上appid，秘钥，通过get方式获取
```java
OPENID_URL="https://api.weixin.qq.com/sns/jscode2session";
Map<String, String> map = new HashMap<>(8);  
map.put("appid", APPID);  
map.put("secret", SECRET);  
map.put("js_code", wechatUser.getJsCode());  
map.put("grant_type", "authorization_code");  
  
String resultJson = HttpClients.get(OPENID_URL, map);
``` 
accessToken获取方法：
也是通过get方法，url不同,获取得到access_token跟expires_in（2小时默认）
```java
String url = TOKEN_URL + "?grant_type=client_credential&appid=" + APP_ID + "&secret=" + APP_SECRET;
String resultJson = HttpClients.get(url)
```
 


## 3.订阅消息
### 接口说明：
[subscribeMessage.send | 微信开放文档 (qq.com)](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/subscribe-message/subscribeMessage.send.html)
### 前提：
登陆小程序，创建好模板
![](https://zpc-1306994356.cos.ap-nanjing.myqcloud.com/ob/202208241031018.png)

获取模板id，字段

data中的类型，都以String类型存进去

Java实现，具体字段参考上面的文档：
```java
String SUBSCRIBE_URL = "https://api.weixin.qq.com/cgi-bin/message/subscribe/send";
String url = SUBSCRIBE_URL + "?access_token=" + token;  
RequestBody body = RequestBody.create(JSON, Jsons.toJson(subMsg));  
String resultJson = HttpClients.post(url, body);
```

可以用postman发送如下的，进行验证(access_token需要替换，touser必须要是该apiiid下生成的，并且已经在小程序端订阅了消息)
```
post发送模板
https://api.weixin.qq.com/cgi-bin/message/subscribe/send?access_token=60_1LE2Afo9K2mvUGh-Oe_SKxKrA8_8HGKZhdg1wgtOj0oq_TI07E883H4ddCqkwtSn53OsKI4gqvj0YIle0RdwE_dwjy-ttci6nuDkH7Ja0zbJmsBa_FL-6lgtYsRZef9fWrNEF5-YgcsIPnd8KALiAGAYOZ

{
  "touser": "oEeL85FWxlG4HJU2eShtLMtWivoM",
  "template_id": "dgiozsRsVCqppuesQHJV5NZW5inmHf5bcNH5C74rglE",
  "page":"/pages/login/index",
  "lang":"zh_CN",
  "data": {
      "character_string5": {
          "value": "12312312333"
      },
      "number13": {
          "value": "11"
      },
      "amount6": {
          "value": "66"
      } ,
      "time3": {
          "value": "2022年08月22日"
      },
      "thing7": {
          "value": "充电结束啦"
      }
  }
}
