# 后置网关路由方案

## 流程

```flow
s=>start: handleMsg
e=>end: success
e2=>end: success
o1=>operation: 解析报文，获取routeTag(原报文标识号)
o2=>operation: 转发到magpie(paraMsgServiceList)
o3=>operation: 转发给fpos(fposNewQueue)
o4=>operation: 转发到magpie(hbcsRouteMsgServiceList)
o6=>operation: 取routeTag[8，10）
o7=>operation: 转发到对应magpie服务列表
c1=>condition: 990报文?
c2=>condition: 参数类报文?
c3=>condition: paraMsgMQFlag
c4=>condition: 只给HBCS的报文
c5=>condition: routeTag内容非空
c6=>condition: 属于通道系统
s->c1(no)->o1->c2(yes)->o2->c3(yes)->o3->e
c3(no)->e
c2(no)->c4(yes)->o4->e
c4(no)->c5(yes)->o6->c6(yes)->o7->e
c6(no)->o3
c5(no)->o3
c1(yes,right)->e2
```

## 配置文件

```yaml
  sysId: HBPG
  routeTag: OrgnlMsgId
  fposNewQueue: MSGFPOSNEW_1
  pmtsQueue: MSGMBFE_1
  hbcsQueue: MSGFPOS_1

  # 参数类报文 路由到所有配置系统
  paraMsgTpList: [
#    "ccms.801.001.02",
#    "ccms.803.001.02",
#    "ccms.807.001.02",
#    "ccms.809.001.02",
#    "ccms.903.001.02",
#    "ccms.906.001.01",
#    "ccms.907.001.02",
#    "ccms.913.001.01",
#    "ccms.915.001.01",
#    "ccms.916.001.01",
#    "ccms.917.001.01"
  ]
  # 参数类报文转发的magpie服务列表
  paraMsgServiceList: ["HBCS_rcvPgMsg01","HBCS_rcvPgMsg51"]
  # 参数类报文是否转发到fposMQ
  paraMsgMQFlag: true

  # 只路由到HBCS的报文类型
  hbcsRouteMsgTpList: [
#    "beps.721.001.01"
  ]
  # 路由到HBCS的magpie服务列表
  hbcsRouteMsgServiceList: ["HBCS_rcvPgMsg01","HBCS_rcvPgMsg51"]

  # 根据routeTag路由 key=routeTag[8,10), value=magpieService 其他情况发送到FPOS MQ
  routeTagServiceMap: {
    # sh hbcs 服务
    01~24:
      ["HBCS_rcvPgMsg01"],
#    # bj hbcs服务
    25~48:
      ["HBCS_rcvPgMsg01"]
  }
```

## 可优化内容

**获取routeTag(原报文标识号)** 在条件内执行
