---
title: 监督检查、抽查数据说明文档
date: 2018-06-26 14:39:35
tags: 监督检查、抽查数据说明文档
---
## 数据存储位置

### /home/nju/neo4j/import/imp_new/check.csv

## 字段说明

#### ID 监督检查表主键
#### PLANTYPE  计划类型,分为国家级监督抽查、省级监督抽查、市级监督抽查、县级监督抽查、其它监督抽查、省级专项监督抽查、市级专项监督抽查、定期监督抽查、定检
#### VERIFYORG  承检机构
#### ORGNAME  企业名称
#### PRODADDR  生产地址
#### REGION  行政区划内容
#### SCALECONTENT  企业规模描述
#### ECONOMYTYPECONTENT  经济类型内容
#### CONMAN  联系人
#### CONTEL  联系电话
#### SINGLEMAN  法人代表
#### ORGANNO  组织机构代码
#### PRODSTATENAME  现场生产状态文字
#### ISREFUSE  企业是否拒检1是0否
#### PRODNAME  产品具体名称
#### MODEL  规格型号
#### PRODUCTTYPE  产品类别
#### STANDARDNAME  检验依据标准(标准编号和名称)
#### PTIDS  标准产品分类
#### BATCHNUM  生产日期或批号
#### SALESVOLUME  销售额（万元）
#### WORKERNUM  人数
#### YEARTOTALOUTPUT  产值（万元）
#### PRODAMOUNT  产量
#### REPORTNO  报告编号
#### ENTITYISPASS  实物质量判定(1合格0不合格)
#### LABELISPASS  标签判定(1合格0不合格)
#### ISPASS  综合判定(1合格0不合格)
#### REMARK  备注
#### STATE  报告状态(0编辑中、1提交2退回)
#### RECHECKORG  复查检验机构名称
#### RECHECKORGCODE  后处理单位行政区划号
#### ISRECHECK  是否已整改
#### ISPUNISH  是否已处罚
#### ISTEST  是否移送检验
#### AFTERDATE  后处理截止时间
#### RECHECKDATE  整改要求完成时间
#### POST  邮政编码
#### BUSLICENO  营业执照注册号
#### CERTNO  许可证编号
#### CCCCERTNO  CCC编号
#### ISCANCEL  是否作废（1作废，0不作废）
#### ISFUJIAN  是否复检（1是，0否）
#### PLANCLASS  计划类别 1监督检查,2监督检验
#### PLANMAKORG  计划编制单位
#### ZXSTANDARDCODE  执行标准编号
#### ZXSTANDARDNAME  执行标准名称
#### ISHISTORY  1是0否  是否历史数据
#### SUBTIME  提交时间
#### ISIMPORTANT  是否严重不合格
#### RZXCODE  认证项
#### QYRYCODE  企业荣誉
#### CYLYCODE  抽样领域
#### RELATIONID  与复检相关连的报告
#### PTIDSNAME  标准产品分类名称
#### ADDR  单位地址
#### PLANNAME  计划编号
#### JYXS  检验项数
#### UNPASSXS  不合格项数
#### UNPASSXM  不合格项目
#### CHECKOKDATE  实际整改完成时间
#### ZGFCDATE  责令整改通知书发出日期
#### ZGTJDATE  企业提交整改报告日期
#### RECHECKSQDATE  复查申请日期
#### RECHECKISPASS  复查是否合格
#### LOGREMARK  后处理移送的备注，然后存入流转历史表的remark
#### CYDATE  抽样日期
#### ZGREMARK  整改信息的备注
#### WFHCL  无法进行后处理
#### RECEIVEDATE  接收移送时间
#### ISYUETAN  是否约谈
#### ISLURU  是否人工录入的记录
#### GRADE  等级
#### SAMPLING_POINT  标准分类
#### SOURCEDEPT  来源部门
#### BATCHNO  检验批号
#### XIANGXIID  国抽表产品分类代码
#### GCEJID  一级产品分类代码
#### GCYJID  二级产品分类代码
#### RETURNOPINION  退回意见
#### MANNAME  处理人
#### BRAND  商标
#### BACKREASON  建议，原因
#### BACKMANNAME  处理人
#### RZRKQK  认证认可情况
#### ZXBZQK  执行标准/技术文件情况
