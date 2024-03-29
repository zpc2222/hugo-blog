---
title: "springboot接入微信支付"
date: 2022-09-26T00:17:58+08:00
lastmod: 2022-09-29T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- java
- 微信支付
description: "小程序使用微信支付（需要商家申请号商户号才能开发）"
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
---

# 微信支付
框架为springboot，需要在小程序的后端服务中，使用接微信支付；
在网上选择了很多的技术选型，发现如果是自己封装，一个是太繁琐，还有就是不方便维护，发现一个比较好的springboot包。

官网地址：‘[Document (notfound403.github.io)](https://notfound403.github.io/payment-spring-boot/#/)’
## Spring boot依赖包
### pom引用

	<dependency>  
    <groupId>cn.felord</groupId>  
    <artifactId>payment-spring-boot-starter</artifactId>  
    <version>1.0.11.RELEASE</version>  
	</dependency>
导入之后，可以直接引用

但是建议直接用别的依赖，如果人家停止维护了，还得重新搞一遍，可以学习下人家的源码，自己封装了下，对接微信支付V3的大部分SDK了
### 源码地址
>GitHub：[GitHub - NotFound403/payment-spring-boot: 微信支付V3，微信优惠券，代金券、公众号支付、微信小程序支付、分账、支付分、商家券](https://github.com/NotFound403/payment-spring-boot)

封装后引用与直接引用效果是一样的
## 实现:

### 1. 预置条件
	1.申请商户号 
	2.绑定小程序与商户号  
	3.在商户号里，生成V3的密钥（V1,V2不要用了，太老了）  
	4.下载p12证书，并导入到项目中`
P12证书导入，编译可能不会成功，需要在pom里面加下：
```
  <plugin>  
    <artifactId>maven-resources-plugin</artifactId>  
    <executions>        <execution>            <id>copy-resources</id>  
            <phase>package</phase>  
            <goals>                <goal>copy-resources</goal>  
            </goals>            <configuration>                <outputDirectory>${project.build.directory}/maven-archiver/resources</outputDirectory>  
                <resources>                    <resource>                        <directory>/src/main/resources/profile-${profile.active}</directory>  
                        <include>application.yml</include>  
                        <include>application-${profile.active}.yml</include>  
                        <include>log4j2-${profile.active}.xml</include>  
                        <include>bootstrap-${profile.active}.properties</include>  
                        <filtering>true</filtering>  
                    </resource>                </resources>            </configuration>        </execution>    </executions>    <configuration>        <encoding>UTF-8</encoding>  
        <!-- 过滤后缀为pem、pfx的证书文件 -->  
        <nonFilteredFileExtensions>  
            <nonFilteredFileExtension>pem</nonFilteredFileExtension>  
            <nonFilteredFileExtension>pfx</nonFilteredFileExtension>  
            <nonFilteredFileExtension>p12</nonFilteredFileExtension>  
        </nonFilteredFileExtensions>    </configuration></plugin>
```
### 2. 添加到工程的yaml配置 
```
wechat:  
  applets: # 小程序的配置项  
    openidUrl: https://api.weixin.qq.com/sns/jscode2session  
    appid: appid  
    secret: secret  
  pay: #微信支付配置项  
    v3:  
      # 微信小程序租户标识为 applets      applets:  
        # 应用appId 必填  
        app-id: appid  
        # api v3 密钥 必填  
        app-v3-secret: v3secret 
        # 微信支付商户号 必填  
        mch-id: 1234567  
        # 商户服务器域名 用于回调  回调路径为 domain + notifyUrl  需要放开回调接口的安全策略 必填  
        domain: https://xxx/app
        # 商户 api 证书路径 必填  填写classpath路径 位于 maven项目的resources文件下  
        cert-path: apiclient_cert.p12
```
### 3. 支付
```
private final WechatApiProvider wechatApiProvider;
  
        OrderInfo orderInfo=XXx
        String ipAddr = getLocalIp();  
        // 定义附加数据  
        Map<String, Object> attach = new HashMap<>();  
        attach.put("userId", orderInfo.getUserId());  
        attach.put("leaveComment", orderInfo.getLeaveComment());  
  
        PayParams payParams = new PayParams();  
        // 商品描述,必填  
        payParams.setDescription(DESC);  
        // 商户侧唯一订单号 建议为商户侧支付订单号 订单表主键 或者唯一标识字段  
        payParams.setOutTradeNo(orderInfo.getOrderId().toString());  
        payParams.setAttach(JSONObject.toJSONString(attach));  
        // 需要定义回调通知  
        payParams.setNotifyUrl(NOTIFY_URL);  
        Amount amount = new Amount();  
        //付款金额单位为“分”  
        amount.setTotal(orderInfo.getTotalPrice().intValue());  
        payParams.setAmount(amount);  
        // 此类支付Payer必传,且openid需要同appid有绑定关系 具体去看文档  
        Payer payer = new Payer();  
        payer.setOpenid(openId);  
        payParams.setPayer(payer);  
        //支付场景描述  
        SceneInfo sceneInfo = new SceneInfo();  
        //用户终端IP  
        sceneInfo.setPayerClientIp(ipAddr);  
        if (orderInfo.getChargePileId() != null) {  
            sceneInfo.setDeviceId(orderInfo.getChargePileId().toString());  
        }  
        payParams.setSceneInfo(sceneInfo);  
        wechatApiProvider.directPayApi(TENANT_ID).jsPay(payParams).getBody();  
```

### 4.支付回调：
 
 ```
 wechatApiProvider.callback(TENANT_ID).transactionCallback(params, data -> {  
    Long orderId = Long.valueOf(data.getOutTradeNo());  
    OrderInfo orderInfo = orderInfoMapper.selectById(orderId);  
    if (orderInfo == null) {  
        log.error("订单不存在，回调异常");  
    }  
    if (UNPAID.getCode() != orderInfo.getStatus()) {  
        log.error("订单状态为[{}]，非未支付，回调异常", orderInfo.getStatus());  
    }  
    orderService.updateOrderStatus(orderId, PAID, OrderInfoVO.builder().payMethod(WXPAY.getCode()).build());  
    log.info("订单状态刷新：orderId={}", orderId);  
    PayRecord payRecord = new PayRecord();  
    payRecord.setPayMerchantid(data.getMchid());  
    payRecord.setPayPrice(data.getAmount().getTotal());  
    payRecord.setOrderId(orderId);  
    payRecord.setType(1);  
    saveRecord(payRecord, 1);  
    log.info("支付记录保存：{}", payRecord);  
});

```

### 5.退款：
```
RefundParams refundParams = new RefundParams();  
RefundParams.RefundAmount amount = new RefundParams.RefundAmount();  
amount.setRefund(paymentRefund.getRefundAmount());  
amount.setTotal(paymentRefund.getActualAmount());  
refundParams.setAmount(amount);  
refundParams.setOutRefundNo(REFUND + paymentRefund.getOutTradeNo());  
refundParams.setNotifyUrl(NOTIFY_REFUND_URL);  
refundParams.setOutTradeNo(paymentRefund.getOutTradeNo());  
refundParams.setReason(paymentRefund.getReason());  
List<RefundGoodsDetail> goodsDetail = new ArrayList<>();  
RefundGoodsDetail detail = new RefundGoodsDetail();  
detail.setMerchantGoodsId(paymentRefund.getMerchantGoodsId());  
detail.setGoodsName(paymentRefund.getGoodsName());  
//数量写死1，商品单价金额等于总退款金额  
detail.setUnitPrice(paymentRefund.getRefundAmount());  
detail.setRefundQuantity(1);  
detail.setRefundAmount(paymentRefund.getRefundAmount());  
goodsDetail.add(detail);  
refundParams.setGoodsDetail(goodsDetail);  
  
wechatApiProvider.directPayApi(TENANT_ID).refund(refundParams).getBody();

```

### 6.退款回调:
```
wechatApiProvider.callback(TENANT_ID).refundCallback(params, data -> {  
    log.info("退款回调：data={}", data);  
    Long orderId = Long.valueOf(data.getOutTradeNo());  
    OrderInfo orderInfo = orderInfoMapper.selectById(orderId);  
    if (orderInfo == null) {  
        log.error("订单[{}]不存在，退款回调异常", orderId);  
    }  
    if (PAID.getCode() != orderInfo.getStatus()) {  
        log.error("订单[{}]状态为[{}]，非已支付，请检查订单状态是否准确", orderId, orderInfo.getStatus());  
        return;    }  
    orderService.updateOrderStatus(orderId, Constant.OrderStatus.REFUNDED, OrderInfoVO.builder().payMethod(WXPAY.getCode()).build());  
    log.info("订单状态刷新：orderId={}", orderId);  
    PayRecord payRecord = new PayRecord();  
    payRecord.setPayMerchantid(data.getMchid());  
    payRecord.setPayPrice(data.getAmount().getPayerRefund());  
    payRecord.setOrderId(orderId);  
    payRecord.setType(2);  
  
    saveRecord(payRecord, 2);  
    log.info("支付记录保存：{}", payRecord);  
});
```

   如上包含了基本支付的一套流程了，还有其他如账单下载，订单查询，关闭订单，积分支付，合并支付等复杂的接口，可以自己研究，封装类里面都已经包含了

---
