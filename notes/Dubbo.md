# Dubboð

Apache Dubbo æ¯ä¸æ¬¾é«æ§è½ãè½»éçº§çå¼æº Java RPC æ¡æ¶ï¼å®æä¾äºä¸å¤§æ ¸å¿è½åï¼é¢åæ¥å£çè¿ç¨æ¹æ³è°ç¨ãæºè½å®¹éåè´è½½åè¡¡ï¼ä»¥åæå¡èªå¨æ³¨åååç°ã



## åèææ¡£

ð  [Dubbo å®æ¹ææ¡£](https://dubbo.apache.org/zh/docs/v2.7/user/)

ð  [Dubbo Github](https://github.com/apache/dubbo)

ð  [Dubbo Springboot Project]()



## æ¶ææ¼å

1. åä¸åºç¨æ¶æ

   å½ç½ç«æµéå¾å°æ¶ï¼åªéä¸ä¸ªåºç¨ï¼å°ææåè½é½é¨ç½²å¨ä¸èµ·ï¼ä»¥åå°é¨ç½²èç¹åææ¬ãæ­¤æ¶ï¼ç¨äºç®åå¢å æ¹æ¥å·¥ä½éçæ°æ®è®¿é®æ¡æ¶ï¼ORMï¼æ¯å³é®ã

2. åç´åºç¨æ¶æ

   å½è®¿é®ééæ¸å¢å¤§ï¼åä¸åºç¨å¢å æºå¨å¸¦æ¥çå éåº¦è¶æ¥è¶å°ï¼å°åºç¨ææäºä¸ç¸å¹²çå ä¸ªåºç¨ï¼ä»¥æåæçãæ­¤æ¶ï¼ç¨äºå éåç«¯é¡µé¢å¼åç Web æ¡æ¶ï¼MVCï¼æ¯å³é®ã

3. åå¸å¼æå¡æ¶æ

   å½åç´åºç¨è¶æ¥è¶å¤ï¼åºç¨ä¹é´äº¤äºä¸å¯é¿åï¼å°æ ¸å¿ä¸å¡æ½ååºæ¥ï¼ä½ä¸ºç¬ç«çæå¡ï¼éæ¸å½¢æç¨³å®çæå¡ä¸­å¿ï¼ä½¿åç«¯åºç¨è½æ´å¿«éçååºå¤åçå¸åºéæ±ãæ­¤æ¶ï¼ç¨äºæé«ä¸å¡å¤ç¨åæ´åçåå¸å¼æå¡æ¡æ¶ (RPC) æ¯å³é®ã



## RPCçæ¦å¿µ

RPCï¼Remote Procedure Callï¼æ¯æè¿ç¨è¿ç¨è°ç¨ï¼æ¯ä¸ç§è¿ç¨é´éä¿¡æ¹å¼ï¼æ¯ä¸ç§ææ¯çææ³ï¼èä¸æ¯è§èã

å®åè®¸ç¨åºè°ç¨å¦ä¸ä¸ªå°åç©ºé´ï¼éå¸¸æ¯å±äº«ç½ç»çå¦ä¸å°æºå¨ä¸ï¼çè¿ç¨æå½æ°ï¼èä¸ç¨ç¨åºåæ¾å¼ç¼ç è¿ä¸ªè¿ç¨è°ç¨çç»èãå³ç¨åºåæ è®ºæ¯è°ç¨æ¬å°çè¿æ¯è¿ç¨çå½æ°ï¼æ¬è´¨ä¸ç¼åçè°ç¨ä»£ç åºæ¬ç¸åã



## RPCä¸HTTP

RPC å HTTP å¹¶ä¸æ¯ä¸ä¸ªå¹¶è¡çæ¦å¿µï¼HTTP æ¯åè®®ï¼RPC æçæ¯è¿ç¨è°ç¨æ¹æ¡ï¼å®åæ¬æ¥å£è§èãåºåååè®®ãéä¿¡åè®®ï¼å¶ä¸­éä¿¡å¯ä»¥ç¨åç§åè®®å®ç°ï¼åæ¬ HTTP ã

RPC æ¯è¿ç«¯è¿ç¨è°ç¨ï¼å¶è°ç¨åè®®éå¸¸åå«ä¼ è¾åè®®ååºåååè®®ã

- ä¼ è¾åè®®ï¼gRPC ä½¿ç¨ç http2 åè®®ï¼dubboä½¿ç¨èªå®ä¹æ¥æç TCP åè®®
- åºåååè®®: å¦åºäºææ¬ç¼ç ç xmlãjsonï¼åºäºäºè¿å¶ç¼ç ç protobufãhessian

<img src="http://store.secretcamp.cn/uPic/image-202108151657500542021081516575016290178706dBVzU6dBVzU.png" alt="image-20210815165750054" style="zoom:50%;" />



åºäºèªå®ä¹ TCP åè®®ç RPC å HTTP è¯·æ±ä¸¤èæå¤§çåºå«èªç¶å¨äºä¼ è¾åè®®ã

HTTP è¯·æ±ä½¿ç¨çæ å TCP åè®®ï¼ä¸èªå®ä¹ç TCP åè®®å¨æ¥æä¸ææåºå«ï¼éç¨å®ä¹ç http 1.1 åè®®ç TCP æ¥æåå«å¤ªå¤æ æä¿¡æ¯ï¼ä¸ä¸ª POST åè®®çæ ¼å¼å¤§è´å¦ä¸ï¼

åæ°æ®ä¹å°±æ¯è¯·æ±å¤´ä½¿ç¨äºææ¬ç¼ç ï¼éå¸¸å å­èæ°ï¼ä½æ¯è¿äºä¿¡æ¯è°ç¨æ¹å¶å®å¹¶ä¸å³å¿ã

```http
HTTP/1.0 200 OK 
Content-Type: text/plain
Content-Length: 137582
Expires: Thu, 05 Dec 1997 16:00:00 GMT
Last-Modified: Wed, 5 August 1996 15:55:28 GMT
Server: Apache 0.84

<html>
  <body>Hello World</body>
</html>
```



èèªå®ä¹ TCP åè®®çæ¥æå¦ä¸ï¼

æ¥æå¤´é¨å ç¨çå­èæ°ä¹å°±åªæ 16 ä¸ª byteï¼æå¤§å°ç²¾ç®äºä¼ è¾åå®¹ã

<img src="http://store.secretcamp.cn/uPic/image-20210815160634386202108151606341629014794rQ5N6krQ5N6k.png" alt="image-20210815160634386" style="zoom:30%;" />



HTTP è°ç¨æ¹å¼ä¸ RPC è°ç¨æ¹å¼çåºå«å¦ä¸ï¼

-  HTTP æç¨ä¿¡æ¯å æ¯å°ï¼æçä½ï¼RPC æç¨ä¿¡æ¯å æ¯é«ï¼æçé«ã
- HTTP å¯è¯»æ§å¥½ï¼RPC å¯è¯»æ§å·®ã
- HTTP è°ç¨è¿ç¨æ¹æ³æ¯è¾å¤æï¼è¦å°è£åç§åæ°åååæ°å¼ï¼RPC å¯ä»¥ä½¿ç¨åå§åæ°ç±»åè°ç¨ã



## RPCçè°ç¨è¿ç¨

æå¡ A å¸æè°ç¨æå¡ B åé¨çæ¹æ³ï¼

1. è°ç¨æ¬å°åçå®ç°

   æå¡ A è°ç¨çæ¯ä¸ä¸ªæ¥å£ï¼å°±å¿é¡»ä¸ºæ¥å£æé ä¸ä¸ªåçå®ç°ï¼æä»¥ä½¿ç¨å¨æä»£çï¼è°ç¨æ¹çè°ç¨å°±è¢«å¨æä»£çæ¥æ¶å°äºã

2. è°ç¨è¿ç¨çæ­£å®ç°

   å¨æä»£çæ¥æ¶å°è°ç¨åï¼è¦è°ç¨è¿ç¨çå®éå®ç°

   - è¯å«å·ä½è¦è°ç¨çè¿ç¨æ¹æ³ç IPãç«¯å£
   - å°è°ç¨æ¹æ³çå¥åè¿è¡åºåå
   - éè¿éä¿¡å°è¯·æ±åéå°è¿ç¨çæ¹æ³ä¸­

3. æå¡æ¹å¤çè¯·æ±

   - ååºåååä¸ªè°ç¨åæ°
   - å®ä½å°å®éè¦è°ç¨çæ¹æ³ï¼ç¶åè¾å¥åæ°ï¼æ§è¡æ¹æ³
   - æç§è°ç¨çè·¯å¾è¿åæ¹æ³è°ç¨çç»æ

<img src="http://store.secretcamp.cn/uPic/image-202108151704366712021081517043616290182769CESBk9CESBk.png" alt="image-20210815170436671" style="zoom:50%;" />



## Dubboè§£å³çé®é¢

1. é«æ§è½ãéæç RPC è°ç¨ï¼Dubbo å¯ä»¥è®©å¼åèåè°ç¨æ¬å°çæ¹æ³ä¸æ ·è°ç¨è¿ç¨æå¡
2. æå¡çèªå¨æ³¨åä¸åç°
   - èªå¨è´è½½ä¸å®¹éï¼Dubbo æä¾äºå®æ´çéç¾¤å®¹éæºå¶ ï¼å¯ä»¥å®ç°è½¯ä»¶å±é¢çè´è½½åè¡¡ ï¼ä»¥æ­¤éä½ç¡¬ ä»¶çåå
   - å¨ææµéè°åº¦ï¼Dubboæä¾äºç®¡çæ§å¶å° ï¼ç¨æ·å¯ä»¥å¨çé¢ä¸å¨æå°è°æ´æ¯ä¸ªæå¡çæé ãè·¯ç±è§åãç¦ç¨/å¯ç¨ï¼å®ç°è¿è¡æ¶çæµéè°åº¦ 
   - ä¾èµåæä¸è°ç¨ç»è®¡ ï¼Dubbo å¯ä»¥ä½¿ç¨ç¬ç«çæ§ä¸­å¿æ¥çæ§æ¥å£çè°ç¨æ¬¡æ°åèæ¶ï¼ç¨æ·å¯ä»¥æ ¹æ®è¿äºæ°æ®åæ¨åºç³»ç»å®¹é 



## Dubboçæ»ä½è°ç¨è¿ç¨



## Dubboåºæ¬æ¶æ

### èç¹è§è²

| èç¹      | è§è²è¯´æ                                                     |
| --------- | ------------------------------------------------------------ |
| Provider  | æ´é²æå¡çæå¡æä¾æ¹ï¼æå¡æä¾èå¨å¯å¨æ¶ï¼åæ³¨åä¸­å¿æ³¨åèªå·±æä¾çæå¡ã |
| Consumer  | è°ç¨è¿ç¨æå¡çæå¡æ¶è´¹æ¹ï¼æå¡æ¶è´¹èå¨å¯å¨æ¶ï¼åæ³¨åä¸­å¿è®¢éèªå·±æéçæå¡ï¼æå¡æ¶è´¹èï¼ä»æä¾èå°ååè¡¨ä¸­ï¼åºäºè½¯è´è½½åè¡¡ç®æ³ï¼éä¸å°æä¾èè¿è¡è°ç¨ï¼å¦æè°ç¨å¤±è´¥ï¼åéå¦ä¸å°è°ç¨ã |
| Registry  | æ³¨åä¸­å¿è¿åæå¡æä¾èå°ååè¡¨ç»æ¶è´¹èï¼å¦ææåæ´ï¼æ³¨åä¸­å¿å°åºäºé¿è¿æ¥æ¨éåæ´æ°æ®ç»æ¶è´¹è |
| Monitor   | æå¡æ¶è´¹èåæä¾èï¼å¨åå­ä¸­ç´¯è®¡è°ç¨æ¬¡æ°åè°ç¨æ¶é´ï¼å®æ¶æ¯åéåéä¸æ¬¡ç»è®¡æ°æ®å°çæ§ä¸­å¿ |
| Container | æå¡è¿è¡å®¹å¨                                                 |





### è°ç¨å³ç³»

1. æå¡å®¹å¨è´è´£å¯å¨ï¼å è½½ï¼è¿è¡æå¡æä¾èã
2. æå¡æä¾èå¨å¯å¨æ¶ï¼åæ³¨åä¸­å¿æ³¨åèªå·±æä¾çæå¡ã
3. æå¡æ¶è´¹èå¨å¯å¨æ¶ï¼åæ³¨åä¸­å¿è®¢éèªå·±æéçæå¡ã
4. æ³¨åä¸­å¿è¿åæå¡æä¾èå°ååè¡¨ç»æ¶è´¹èï¼å¦ææåæ´ï¼æ³¨åä¸­å¿å°åºäºé¿è¿æ¥æ¨éåæ´æ°æ®ç»æ¶è´¹èã
5. æå¡æ¶è´¹èï¼ä»æä¾èå°ååè¡¨ä¸­ï¼åºäºè½¯è´è½½åè¡¡ç®æ³ï¼éä¸å°æä¾èè¿è¡è°ç¨ï¼å¦æè°ç¨å¤±è´¥ï¼åéå¦ä¸å°è°ç¨ã
6. æå¡æ¶è´¹èåæä¾èï¼å¨åå­ä¸­ç´¯è®¡è°ç¨æ¬¡æ°åè°ç¨æ¶é´ï¼å®æ¶æ¯åéåéä¸æ¬¡ç»è®¡æ°æ®å°çæ§ä¸­å¿ã

<img src="http://store.secretcamp.cn/uPic/image-20210522093600720202105220936001621647360xKWKeexKWKee.png" alt="image-20210522093600720" style="zoom:50%;" />







# Dubboç¸å³æ¦å¿µð

## æ³¨åä¸­å¿

Dubbo æ¯æäºç§[æ³¨åä¸­å¿](https://dubbo.apache.org/zh/docs/v2.7/user/references/registry/)



## æå¡åç°

1. å¯å¨ï¼Provider ç»å®æå®ç«¯å£å¹¶å¯å¨æå¡

2. æ³¨åæå¡ï¼Provider è¿æ¥æ³¨åä¸­å¿ï¼å°æ¬æº IPãç«¯å£ãåºç¨ä¿¡æ¯åæä¾æå¡ä¿¡æ¯åéè³æ³¨åä¸­å¿å­å¨

3. è®¢éæå¡ï¼Consumer è¿æ¥æ³¨åä¸­å¿ ï¼åéåºç¨ä¿¡æ¯ãææ±æå¡ä¿¡æ¯è³æ³¨åä¸­å¿

4. æå¡è®¢éæåæ´æ¶ï¼æ¨éæå¡å°ååè¡¨

   æ³¨åä¸­å¿æ ¹æ® Consumer è¯·æ±çæå¡ä¿¡æ¯å¹éå¯¹åºç Provider åè¡¨ï¼å¹¶åéè³ Consumer åºç¨ç¼å­ ï¼Provider ç¶æåæ´ä¼å®æ¶éç¥æ³¨åä¸­å¿ï¼ç¶åæ³¨åä¸­å¿å®æ¶æ¨éè³ Consumer

5. è°ç¨æå¡ï¼Consumer å¨åèµ·è¿ç¨è°ç¨æ¶ï¼éæ©åºäºç¼å­ç Provider åè¡¨ä¸­çä¸ä¸ª Provider çå°åï¼åºäºæééæºéæ©ä¸ä¸ªåèµ·è°ç¨
   
6. Dubbo åå°å®æ¶ééæå¡è°ç¨æ¬¡æ°åè°ç¨æ¶é´ç­ä¿¡æ¯




## æå¡åæ´

æå¡åæ´ä¾èµäº Zookeeper ç Watch æºå¶ã

Consumer ä¼å  Zookeeper æ³¨åä¸ä¸ª watcher çå¬æä¸èç¹ï¼Zookeeper ä¼å Provider ä¿æå¿è·³ï¼å®éä¸å»ºç«çæ¯ä¸ä¸ª Socket é¿è¿æ¥ï¼ï¼å¦æ Provider å¤±èï¼å¯¹åºç node ä¼è¢«å é¤ï¼æèæ°ç Provider ä¸çº¿ä¼è¯·æ± Zookeeperï¼å¨æä¸ serviceName ä¸å»ºç«ä¸ä¸ªæ°çèç¹ãäºæ¯ /dubbo/serviceName åçåå¨ï¼è§¦å Watch æºå¶ï¼æ¶è´¹èç«¯å·æ° Provider ä¿¡æ¯ã




# Dubboéç½®ð

## éç½®ä¼åçº§

ç¬¬ä¸ä¼åçº§ï¼JVM ç -D å¯å¨åæ°

ç¬¬äºä¼åçº§ï¼xml æä»¶ä¸­çéç½®

ç¬¬ä¸ä¼åçº§ï¼dubbo.properties ä¸­çéç½®





## XMLéç½®

| æ ç­¾                   | ç¨é         | è§£é                                                         |
| ---------------------- | ------------ | ------------------------------------------------------------ |
| `<dubbo:service/>`     | æå¡éç½®     | ç¨äºæ´é²ä¸ä¸ªæå¡ï¼å®ä¹æå¡çåä¿¡æ¯ï¼ä¸ä¸ªæå¡å¯ä»¥ç¨å¤ä¸ªåè®®æ´é²ï¼ä¸ä¸ªæå¡ä¹å¯ä»¥æ³¨åå°å¤ä¸ªæ³¨åä¸­å¿ |
| `<dubbo:reference/>`   | å¼ç¨éç½®     | ç¨äºåå»ºä¸ä¸ªè¿ç¨æå¡ä»£çï¼ä¸ä¸ªå¼ç¨å¯ä»¥æåå¤ä¸ªæ³¨åä¸­å¿       |
| `<dubbo:protocol/>`    | åè®®éç½®     | ç¨äºéç½®æä¾æå¡çåè®®ä¿¡æ¯ï¼åè®®ç±æä¾æ¹æå®ï¼æ¶è´¹æ¹è¢«å¨æ¥å |
| `<dubbo:application/>` | åºç¨éç½®     | ç¨äºéç½®å½ååºç¨ä¿¡æ¯ï¼ä¸ç®¡è¯¥åºç¨æ¯æä¾èè¿æ¯æ¶è´¹è           |
| `<dubbo:module/>`      | æ¨¡åéç½®     | ç¨äºéç½®å½åæ¨¡åä¿¡æ¯ï¼å¯é                                   |
| `<dubbo:registry/>`    | æ³¨åä¸­å¿éç½® | ç¨äºéç½®è¿æ¥æ³¨åä¸­å¿ç¸å³ä¿¡æ¯                                 |
| `<dubbo:monitor/>`     | çæ§ä¸­å¿éç½® | ç¨äºéç½®è¿æ¥çæ§ä¸­å¿ç¸å³ä¿¡æ¯ï¼å¯é                           |
| `<dubbo:provider/>`    | æä¾æ¹éç½®   | å½ ProtocolConfig å ServiceConfig æå±æ§æ²¡æéç½®æ¶ï¼éç¨æ­¤ç¼ºçå¼ï¼å¯é |
| `<dubbo:consumer/>`    | æ¶è´¹æ¹éç½®   | å½ ReferenceConfig æå±æ§æ²¡æéç½®æ¶ï¼éç¨æ­¤ç¼ºçå¼ï¼å¯é      |
| `<dubbo:method/>`      | æ¹æ³éç½®     | ç¨äº ServiceConfig å ReferenceConfig æå®æ¹æ³çº§çéç½®ä¿¡æ¯   |
| `<dubbo:argument/>`    | åæ°éç½®     | ç¨äºæå®æ¹æ³åæ°éç½®                                         |



## å¨æéç½®ä¸­å¿

æ¯ä¸ªæå¡çéç½®é½åå¨èªå·±çéç½®æä»¶ä¸­ï¼æ´æ°éç½®æ¶è¦ä¸å°æºå¨ä¸å°æºå¨å°ä¿®æ¹ï¼å¾ä¸æ¹ä¾¿





## åæ°æ®ä¸­å¿

åæ°æ®ä¸­å¿ç¨äºå­å¨ä¸äºæå¡æä¾èãæ¶è´¹èçä¿¡æ¯ï¼æ¯å¦dubboçæ¬ãæå¡æ¥å£çä¿¡æ¯ï¼åæ¬æ¹æ³åãå½¢åè¡¨ãè¿åå¼ç±»åï¼ç­ç­ã

## å±æ§éç½®

Dubbo å¯ä»¥èªå¨å è½½ classpath æ ¹ç®å½ä¸ç `dubbo.properties`



## APIéç½®



## æ³¨è§£éç½®

### @EnableDubbo

`@EnableDubbo` æ´åäºä¸¤ä¸ªæ³¨è§£ï¼

- `@EnableDubboConfig`

  å¼å¥äº `DubboConfigConfigurationRegistrar` ç±»ï¼ç¨äºè§£æéç½®ç¸å³çç±»æ³¨åå° spring å®¹å¨

- `@DubboComponentScan`

  å¼å¥äº `DubboComponentScanRegistrar` ç±»ï¼ç¨äºæå® `@DubboService` çæ«æè·¯å¾

 

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@EnableDubboConfig
@DubboComponentScan
public @interface EnableDubbo {
    @AliasFor(
        annotation = DubboComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};

    @AliasFor(
        annotation = DubboComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};

    @AliasFor(
        annotation = EnableDubboConfig.class,
        attribute = "multiple"
    )
    boolean multipleConfig() default true;
}
```



### @DubboService

ç¸å½äº `<dubbo:service/>` æ ç­¾ï¼ä¹å·æå°æ æ³¨çç±»å å¥ ioc å®¹å¨çä½ç¨ã



### @DubboReference

ç¸å½äº `<dubbo:reference/>` æ ç­¾ï¼å°è¿ç¨å®ç°ç bean å å¥ ioc å®¹å¨ï¼åæ¶åå®æä¾èµæ³¨å¥çè¿ç¨ã



### @Method



# æä½³å®è·µð

## å¤±è´¥éè¯

å¤±è´¥èªå¨åæ¢ï¼å½åºç°å¤±è´¥ï¼éè¯å¶å®æå¡å¨ï¼ä½éè¯ä¼å¸¦æ¥æ´é¿å»¶è¿ã

```xml
<!-- æå¡ç«¯éç½®-->
<dubbo:service retries="3" />


<!-- æ¶è´¹ç«¯éç½®-->
<dubbo:reference retries="3" />

<dubbo:reference>
    <dubbo:method name="findFoo" retries="3" />
</dubbo:reference>
```



## è¶æ¶æ¶é´

ç±äºç½ç»ææå¡ç«¯ä¸å¯é ï¼ä¼å¯¼è´è°ç¨åºç°ä¸ç§ä¸ç¡®å®çä¸­é´ç¶æï¼è¶æ¶ï¼ãä¸ºäºé¿åè¶æ¶å¯¼è´å®¢æ·ç«¯èµæºï¼çº¿ç¨ï¼æèµ·èå°½ï¼å¿é¡»è®¾ç½®è¶æ¶æ¶é´ã

1. éç½®åå

   dubboæ¨èå¨Providerä¸å°½éå¤éç½®Consumerç«¯å±æ§ï¼

   - ä½ä¸º Providerï¼æ¯ Consumer æ´æ¸æ¥æå¡æ§è½åæ°ï¼å¦è°ç¨çè¶æ¶æ¶é´ï¼åççéè¯æ¬¡æ°
   - å¨ Provider éç½®åï¼Consumerä¸éç½®åä¼ä½¿ç¨ Provider çéç½®å¼ï¼å³Provideréç½®å¯ä»¥ä½ä¸ºConsumerçç¼ºçå¼ãå¦åï¼Consumer ä¼ä½¿ç¨ Consumer ç«¯çå¨å±è®¾ç½®ï¼è¿å¯¹äº Provider ä¸å¯æ§çï¼å¹¶ä¸å¾å¾æ¯ä¸åçç



2. æå¡æä¾è

   ```xml
   <!-- å¨å±è¶æ¶éç½®-->
   <dubbo:provider timeout="5000" />
   
   <!-- æå®æ¥å£ä»¥åç¹å®æ¹æ³è¶æ¶éç½® -->
   <dubbo:provider interface="com.foo.BarService" timeout="2000">
       <dubbo:method name="sayHello" timeout="3000" />
   </dubbo:provider>
   ```



3. æå¡æ¶è´¹è

   ```xml
   <!-- å¨å±è¶æ¶éç½®-->
   <dubbo:consumer timeout="5000" />
   
   <!-- æå®æ¥å£ä»¥åç¹å®æ¹æ³è¶æ¶éç½® -->
   <dubbo:reference interface="com.foo.BarService" timeout="2000">
       <dubbo:method name="sayHello" timeout="3000" />
   </dubbo:reference>
   ```

   

## å¤çæ¬

å½ä¸ä¸ªæ¥å£å®ç°ï¼åºç°ä¸å¼å®¹åçº§æ¶ï¼å¯ä»¥ç¨çæ¬å·è¿æ¸¡ï¼çæ¬å·ä¸åçæå¡ç¸äºé´ä¸å¼ç¨ã

```xml
<!-- èçæ¬æå¡æä¾èéç½® -->
<dubbo:service interface="com.foo.BarService" version="1.0.0" />

<!-- æ°çæ¬æå¡æä¾èéç½® -->
<dubbo:service interface="com.foo.BarService" version="2.0.0" />

<!-- èçæ¬æå¡æ¶è´¹èéç½® -->
<dubbo:reference id="barService" interface="com.foo.BarService" version="1.0.0" />

<!-- æ°çæ¬æå¡æ¶è´¹èéç½® -->
<dubbo:reference id="barService" interface="com.foo.BarService" version="2.0.0" />

```



## æ¬å°å­æ ¹





# Dubboé«å¯ç¨ð

## æ¬å°ç¼å­éè®¯ä¸ç´è¿

Zookeeper ä½ä¸ºæ³¨åä¸­å¿å¦æå®æºï¼æå¡æ¶è´¹èä»ç¶å¯ä»¥è°ç¨ dubbo æ´é²çæå¡

- æå¡æä¾èåæå¡æ¶è´¹èä»è½éè¿æ¬å°ç¼å­éè®¯

- æå¡æä¾èåæå¡æ¶è´¹èå¯ä»¥éè¿ç´è¿çæ¹å¼éè®¯ï¼ä¸ç»è¿æ³¨åä¸­å¿

```xml
<dubbo:reference id="xxxService" interface="com.alibaba.xxx.XxxService" url="dubbo://localhost:20890" />
```



## è´è½½åè¡¡

å¨éç¾¤è´è½½åè¡¡æ¶ï¼Dubbo æä¾äºå¤ç§åè¡¡ç­ç¥ï¼é»è®¤ä¸º random éæºè°ç¨

- Random LoadBalanceï¼ææééæºï¼
- RoundRobin LoadBalanceï¼ææéè½®è¯¢ï¼
- LeastActive LoadBalanceï¼æå°æ´»è·è°ç¨ï¼
- ConsistentHash LoadBalanceï¼ä¸è´æ§åå¸ï¼



## éç¾¤å®¹é

å¨éç¾¤è°ç¨å¤±è´¥æ¶ï¼Dubbo æä¾äºå¤ç§å®¹éæ¹æ¡ï¼é»è®¤ä¸º Failover éè¯ã

- Failover Clusterï¼å¤±è´¥èªå¨åæ¢ï¼å½åºç°å¤±è´¥ï¼éè¯å¶å®æå¡å¨ï¼å¯ä»¥è®¾ç½®éè¯æ¬¡æ°ã
- Failfast Clusterï¼å¿«éå¤±è´¥ï¼åªåèµ·ä¸æ¬¡è°ç¨ï¼å¤±è´¥ç«å³æ¥éã
- Failsafe Clusterï¼å¤±è´¥å®å¨ï¼åºç°å¼å¸¸æ¶ï¼ç´æ¥å¿½ç¥ãéå¸¸ç¨äºåå¥å®¡è®¡æ¥å¿ç­æä½ã
- Failback Clusterï¼å¤±è´¥èªå¨æ¢å¤ï¼åå°è®°å½å¤±è´¥è¯·æ±ï¼å®æ¶éåï¼éå¸¸ç¨äºæ¶æ¯éç¥æä½ã
- Forking Clusterï¼å¹¶è¡è°ç¨å¤ä¸ªæå¡å¨ï¼åªè¦ä¸ä¸ªæåå³è¿åï¼éå¸¸ç¨äºå®æ¶æ§è¦æ±è¾é«çè¯»æä½ï¼ä½éè¦æµªè´¹æ´å¤æå¡èµæºã
- Broadcast Clusterï¼å¹¿æ­è°ç¨æææä¾èï¼éä¸ªè°ç¨ï¼ä»»æä¸å°æ¥éåæ¥éï¼éå¸¸ç¨äºéç¥æææä¾èæ´æ°ç¼å­ææ¥å¿ç­æ¬å°èµæºä¿¡æ¯ã



## æå¡éçº§

å½æå¡å¨ååå§å¢çæåµä¸ï¼æ ¹æ®å®éä¸å¡æåµåæµéï¼å¯¹ä¸äºæå¡åé¡µé¢æç­ç¥çä¸å¤çææ¢ç§ç®åçæ¹å¼å¤çï¼ä»èéæ¾æå¡å¨èµæºä»¥ä¿è¯æ ¸å¿äº¤ææ­£å¸¸è¿ä½æé«æè¿ä½ã



# çæ§ä¸­å¿ð

## Dubbo-Admin

githubï¼https://github.com/apache/dubbo-admin



### dubbo-admin-ui

å¯å¨åèï¼https://www.cnblogs.com/yxth/p/11819874.html

ä¿®æ¹åç«¯éç½®æä»¶ `dubbo-admin-ui/vue.config.js`

```
port: 8021  # åç«¯é¡¹ç®å°åï¼å³æµè§å¨ä¸­è¦è®¿é®çç«¯å£

target: 'http://120.79.198.81:8082/'   # åç«¯é¡¹ç®å°å
```

å¨ dubbo-admin-ui ä¸­æ§è¡ä»¥ä¸å½ä»¤è¿è¡åç«¯æå¡

```sh
npm install
npm run dev
```





### dubbo-admin-server

å¨ dubbo-admin-server/src/main/resources/application.properties ä¸­ä¿®æ¹éç½®æä»¶

```properties
server.port=8082
admin.registry.address=zookeeper://120.79.198.81:2181?backup=120.79.198.81:2182,120.79.198.81:2183
admin.config-center=zookeeper://120.79.198.81:2181?backup=120.79.198.81:2182,120.79.198.81:2183
admin.metadata-report.address=zookeeper://120.79.198.81:2181?backup=120.79.198.81:2182,120.79.198.81:2183
```



å¨ dubbo-admin-server ä¸­æå»ºåç«¯æå¡

```sh
mvn clean package -Dmaven.test.skip=true
```

ç¶åè¿è¡æå»ºå¥½ç jar å

```sh
java -jar target/dubbo-admin-server-0.3.0-SNAPSHOT.jar
```

æè

```sh
mvn --projects dubbo-admin-server spring-boot:run
```



## Dubbo-Monitor







# Dubboåçð





# Dubbo-Springð

## é¡¹ç®æå»º

### é¡¹ç®æ¶æ

```ruby
dubbo-learn-xml
     |-------- interface-api
     |-------- dubbo-provider-user
     |-------- dubbo-provider-order
     |-------- dubbo-consumer
```



### ä¾èµ

1. ç¶é¡¹ç®

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>kzq.yorua</groupId>
       <artifactId>dubbo-learn-xml</artifactId>
       <packaging>pom</packaging>
       <version>1.0-SNAPSHOT</version>
       <modules>
           <module>dubbo-interface</module>
           <module>dubbo-provider-user</module>
           <module>dubbo-provider-order</module>
           <module>dubbo-consumer</module>
       </modules>
       <dependencies>
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-context</artifactId>
               <version>5.2.15.RELEASE</version>
           </dependency>
       </dependencies>
   </project>
   ```

   

2. æå¡æä¾è

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-learn-xml</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-provider-order</artifactId>
       <dependencies>
           <!-- API æ¥å£-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo</artifactId>
               <version>2.7.8</version>
           </dependency>
          <!-- Zookeeper-->
           <dependency>
               <groupId>org.apache.zookeeper</groupId>
               <artifactId>zookeeper</artifactId>
               <version>3.4.10</version>
           </dependency>       
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

3. æå¡æ¶è´¹è

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns="http://maven.apache.org/POM/4.0.0"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-learn-xml</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-consumer</artifactId>
       <dependencies>
           <!-- API æ¥å£-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo</artifactId>
               <version>2.7.8</version>
           </dependency>
          <!-- Zookeeper-->
           <dependency>
               <groupId>org.apache.zookeeper</groupId>
               <artifactId>zookeeper</artifactId>
               <version>3.4.10</version>
           </dependency>      
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

## å®ä¹æå¡æ¥å£ 

```java
public interface UserService {
    User queryUserById(int id);
}

public interface OrderService {
    String sendOrder(int id);
}
```



## æå¡æä¾è

è®¾ç½®ä¸¤ä¸ªæå¡æä¾èï¼UserService å OrderServiceï¼å¶ä¸­ OrderService ä¸ä»ä¼æä¾èªèº«çæå¡ï¼ä¹ä¼æ¶è´¹ UserService çæå¡ï¼å®ä¾èµäº UserServiceã

### æå¡æä¾æ¹å®ç°æ¥å£

UserService æå¡çå®ç°

```java
public class UserServiceImpl implements UserService {

    @Override
    public User queryUserById(int id) {
        User user = null;
        if (id == 1) user = new User(1, "èå«", "æ¤ç¡");
        else if (id == 2) user = new User(2, "æ½å­", "é¿æ±");
        else if (id == 3) user = new User(3, "åå­", "ç½æ´æ·");
        else user = new User(4, "é©¬èå¸", "å¤å½");
        return user;
    }
}
```



OrderService æå¡çå®ç°ï¼è¯¥å®ç°ä¼è¿ç¨è°ç¨ UserService æå¡çå®ç°

```java
public class OrderServiceImpl implements OrderService {
		// è¿ééè¦è°ç¨ userService çè¿ç¨å®ç°
    UserService userService;

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public String sendOrder(int id) {
        User user = userService.queryUserById(id);
        String msg = "Hello " + user.getName() + ", this is your order.";
        return msg;
    }
}
```





### éç½®å£°ææ´é²æå¡

UserService æå¡éç½®

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo
       http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- æä¾æ¹åºç¨ä¿¡æ¯ï¼ç¨äºè®¡ç®ä¾èµå³ç³» -->
    <dubbo:application name="user-provider-app" id="user-provider-app"/>

    <!-- ä½¿ç¨ zookeeper å¹¿æ­æ³¨åä¸­å¿æ´é²æå¡å°å -->
    <dubbo:registry protocol="zookeeper" address="120.79.198.81:2181,120.79.198.81:2182,120.79.198.81:2183"/>

    <!-- æå®éä¿¡è§ååéä¿¡ç«¯å£ ç¨ dubbo åè®®å¨ 20880 ç«¯å£æ´é²æå¡ -->
    <dubbo:protocol name="dubbo" port="20880"/>

    <!-- å° UserService çæ¬å°å®ç°æ³¨å¥å®¹å¨ -->
    <bean id="userServiceImpl" class="userprovider.service.impl.UserServiceImpl"/>

    <!-- å£°æéè¦æ´é²çæå¡æ¥å£ï¼å¼ç¨æ¬å°å®ç°  -->
    <dubbo:service interface="interf.service.UserService" ref="userServiceImpl"/>

</beans>
```



OrderService æå¡éç½®

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo
       http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- æä¾æ¹åºç¨ä¿¡æ¯ï¼ç¨äºè®¡ç®ä¾èµå³ç³» -->
    <dubbo:application name="order-provider-app"/>

    <!-- ä½¿ç¨ zookeeper æ³¨åä¸­å¿æ´é²æå¡å°å -->
    <dubbo:registry protocol="zookeeper" address="120.79.198.81:2181,120.79.198.81:2182,120.79.198.81:2183"/>

    <!-- æå®éä¿¡è§ååéä¿¡ç«¯å£ ç¨ dubbo åè®®å¨ 20881 ç«¯å£æ´é²æå¡ -->
    <dubbo:protocol name="dubbo" port="20881"/>
    
    <!-- çæè¿ç¨æå¡ä»£çï¼å°è¿ç¨æå¡çå®ç°æ³¨å¥æ¬å°å®¹å¨ï¼å¯ä»¥åæ¬å° bean ä¸æ ·ä½¿ç¨ -->
    <!-- è¿ç¨æå¡ç»ä»¶ç id å³ä¸ºæ ç­¾ä¸­ç id å¼ -->
    <dubbo:reference id="userServiceImpl" interface="interf.service.UserService"/>

    <!-- å° UserService çè¿ç¨å®ç°ï¼æ¬å° beanï¼æ³¨å¥ OrderService çæ¬å°å®ç° -->
    <bean id="orderServiceImpl" class="orderprovider.service.impl.OrderServiceImpl">
        <!--suppress InjectionValueTypeInspection -->
        <property name="userService" ref="userServiceImpl"/>
    </bean>

    <!-- å£°æéè¦æ´é²çæå¡æ¥å£ -->
    <dubbo:service interface="interf.service.OrderService" ref="orderServiceImpl"/>

</beans>
```



### å è½½éç½®æä¾è¿ç¨æå¡

```java
public class UserProviderApplication {

    public static void main(String[] args) throws IOException {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring-user.xml");
        System.in.read();
    }
}

public class OrderProviderApplication {

    public static void main(String[] args) throws IOException {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring-order.xml");
        System.in.read();
    }
}
```



## æå¡æ¶è´¹è

è®¾ç½®ä¸ä¸ªæ¶è´¹èï¼æ¶è´¹ OrderService æä¾çæå¡

### éç½®å¼ç¨è¿ç¨æå¡

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
       http://dubbo.apache.org/schema/dubbo
       http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- æ¶è´¹æ¹åºç¨åï¼ç¨äºè®¡ç®ä¾èµå³ç³»ï¼ä¸æ¯å¹éæ¡ä»¶ï¼ä¸è¦ä¸æä¾æ¹ä¸æ · -->
    <dubbo:application name="consumer-app"/>

    <!-- ä½¿ç¨ zookeeper å¹¿æ­æ³¨åä¸­å¿æ´é²æå¡å°å -->
    <dubbo:registry protocol="zookeeper" address="120.79.198.81:2181,120.79.198.81:2182,120.79.198.81:2183"/>

    <!-- çæè¿ç¨æå¡ä»£çï¼å° OrderService çè¿ç¨å®ç°æ³¨å¥æ¬å° ioc å®¹å¨ -->
    <dubbo:reference id="orderServiceImpl" interface="interf.service.OrderService"/>

</beans>
```



### å è½½éç½®è°ç¨è¿ç¨æå¡

```java
public class ConsumerApplication {
    public static void main(String[] args) throws IOException {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");
        OrderService orderService = (OrderService) ac.getBean("orderServiceImpl");
        String s = orderService.sendOrder(1);
        System.in.read(); // æä»»æé®éåº
    }
}
```







# Dubbo-Springbootð

## é¡¹ç®æå»º

Springboot æ´å Dubbo ä¸»è¦æä¸ç§æ¹å¼ï¼

1. å¨ application.propertirs ä¸­åå¥ Dubbo ç¸å³éç½®
2. ä½¿ç¨ `@ImportResource` å¼å¥ Dubbo ç xml éç½®
3. ä½¿ç¨éç½®æä»¶ç±»ç API è®¾ç½® ï¼åæå¨å°éç½®æä»¶æ³¨å¥å®¹å¨

ä»¥ä¸åªæ¼ç¤ºæä¸ºå¸¸ç¨çç¬¬ä¸ç§æ¹å¼



### é¡¹ç®æ¶æ

```ruby
spring-dubbo
     |-------- interface-api
     |-------- dubbo-provider-user
     |-------- dubbo-provider-order
     |-------- dubbo-consumer
```



### ä¾èµ

1. ç¶é¡¹ç®

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>kzq.yorua</groupId>
       <artifactId>dubbo-springboot-learn</artifactId>
       <packaging>pom</packaging>
       <version>1.0-SNAPSHOT</version>
       <parent>
           <artifactId>spring-boot-starter-parent</artifactId>
           <groupId>org.springframework.boot</groupId>
           <version>2.4.5</version>
       </parent>
       <modules>
           <module>interface-api</module>
           <module>dubbo-provider-user</module>
           <module>dubbo-provider-order</module>
           <module>dubbo-consumer</module>
       </modules>
       <dependencies>
           <!-- Springboot-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter</artifactId>
               <version>2.4.5</version>
           </dependency>
       </dependencies>
   </project>
   ```

   

2. æå¡æä¾è

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-springboot-learn</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-provider-order</artifactId>
       <dependencies>
           <!-- API æ¥å£-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo-spring-boot-starter</artifactId>
               <version>2.7.8</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

3. æå¡æ¶è´¹è

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns="http://maven.apache.org/POM/4.0.0"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dubbo-springboot-learn</artifactId>
           <groupId>kzq.yorua</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>dubbo-consumer</artifactId>
       <dependencies>
           <!-- API æ¥å£-->
           <dependency>
               <groupId>kzq.yorua</groupId>
               <artifactId>interface-api</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
           <!-- Web-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <!-- Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo-spring-boot-starter</artifactId>
               <version>2.7.8</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

   

## å®ä¹æå¡æ¥å£

è¯¥æ¨¡åç¨äºæä¾æå¡æ¥å£

```java
public interface UserService {
    User queryUserById(int id);
}

public interface OrderService {
    String sendOrder(int id);
}
```



## æå¡æä¾è

è®¾ç½®ä¸¤ä¸ªæå¡æä¾èï¼UserService å OrderServiceï¼å¶ä¸­ OrderService ä¸ä»ä¼æä¾èªèº«çæå¡ï¼ä¹ä¼æ¶è´¹ UserService çæå¡ï¼å®ä¾èµäº UserServiceã

### æå¡æä¾æ¹å®ç°æ¥å£

UserService æå¡çå®ç°

```java
@DubboService(interfaceClass = UserService.class)
public class UserServiceImpl implements UserService {

    @Override
    public User queryUserById(int id) {
        User user = null;
        if (id == 1) user = new User(1, "èå«", "æ¤ç¡");
        else if (id == 2) user = new User(2, "æ½å­", "é¿æ±");
        else if (id == 3) user = new User(3, "åå­", "ç½æ´æ·");
        else user = new User(4, "é©¬èå¸", "å¤å½");
        return user;
    }
}
```



OrderService æå¡çå®ç°ï¼è¯¥å®ç°ä¼è¿ç¨è°ç¨ UserService æå¡çå®ç°

```java
@DubboService(interfaceClass = OrderService.class)
public class OrderServiceImpl implements OrderService {
    @DubboReference
    UserService userService;

    @Override
    public String sendOrder(int id) {
        User user = userService.queryUserById(id);
        String msg = "Hello " + user.getName() + ", this is your order.";
        return msg;
    }
}
```



### éç½®å£°ææ´é²æå¡

UserService æå¡éç½®

```properties
spring.application.name=dubbo-provider-user
dubbo.application.name=dubbo-provider-user
dubbo.application.id=dubbo-provider-user
dubbo.registry.protocol=zookeeper
dubbo.registry.address=zookeeper://120.79.198.81:2181;zookeeper://120.79.198.81:2182;zookeeper://120.79.198.81:2183
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
```



OrderService æå¡éç½®

```properties
spring.application.name=dubbo-provider-order
dubbo.application.name=dubbo-provider-order
dubbo.application.id=dubbo-provider-order
dubbo.registry.protocol=zookeeper
dubbo.registry.address=zookeeper://120.79.198.81:2181;zookeeper://120.79.198.81:2182;zookeeper://120.79.198.81:2183
dubbo.protocol.name=dubbo
dubbo.protocol.port=20881
```



### å¯å¨é¡¹ç®

åå«å¯å¨ dubbo-provider-user å dubbo-provider-order çæå¡

```java
@EnableDubbo
@SpringBootApplication
public class UserServiceProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserServiceProviderApplication.class, args);
    }
}

@EnableDubbo
@SpringBootApplication
public class OrderServiceProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderServiceProviderApplication.class, args);
    }
}
```





## æå¡æ¶è´¹è

### éç½®å¼ç¨è¿ç¨æå¡

```properties
spring.application.name=dubbo-demo-consumer
dubbo.application.name=dubbo-demo-consumer
dubbo.application.id=dubbo-demo-consumer
dubbo.registry.address=zookeeper://120.79.198.81:2181;zookeeper://120.79.198.81:2182;zookeeper://120.79.198.81:2183
```



### è°ç¨è¿ç¨æå¡

```java
@RestController
public class OrderController {
    @DubboReference
    OrderService orderService;

    @RequestMapping("/order")
    public String showOrder(@RequestParam("id") int id) {
        return orderService.sendOrder(id);

    }
}
```



### å¯å¨é¡¹ç®

```java
@SpringBootApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```





