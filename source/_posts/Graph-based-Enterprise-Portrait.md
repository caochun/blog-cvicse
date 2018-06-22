---
title: 基于图数据库的企业画像
date: 2018-06-21 12:39:26
tags:
---

基于图数据库的企业画像项目基于开源Neo4j图数据库构建复杂企业用户画像和关系图谱，为质监等行业提供快速高效的企业信息发掘和其他业务支撑。

### 项目背景

### 技术方案

#### Neo4j图模型

##### 环境配置

Neo4j部署在一台云服务器上，操作系统为centOS 7。
Neo4j需要java环境。

``` bash
$ yum install java
```

之后，下载并解压Neo4j，考虑到需要远程访问，需要修改配置文件`conf/neo4j.conf`。

```
dbms.connectors.default_listen_address=0.0.0.0
dbms.connectors.default_advertised_address=0.0.0.0
```

另外配置系统允许打开的最大文件数，修改配置文件`/etc/security/limits.conf`。

```
root   soft    nofile  40000
root   hard    nofile  40000
```

现在就可以正常启动Neo4j了

``` bash
$ bin/neo4j start
```

并且可以从本机的7474端口访问Neo4j的web UI，初始登录账户密码都是neo4j。

因为当前环境服务器在NAT之后，无法直接访问，需要做端口映射，把Neo4j的7474端口暴露出来。
也可以通过ssh建立socks代理，直接访问服务器ip。

``` bash
$ ssh -D 8888 nju@210.28.133.11 -p 21084
```

之后就可以在浏览器的代理设置中使用socks代理，设置主机为127.0.0.1，端口为8888（或者设为其他可用的端口），从而直接访问Neo4j服务器的7474端口。

#### 数据导入与模型构建

##### 模型示例

根据客户需求，提取出一个简单的模型验证技术路线。
该模型从企业的角度出发，包含高管、法人等关系。

![](/images/graph/model.png)

从Neo4j的角度来看，上述模型包含`法人`、`企业`、`产品`、`高管`等节点，包含`企业生产产品`等关系。

##### 数据准备

根据上述模型，从原关系型数据库中，提取出相关的数据，具体过程如下

``` SQL
select * from M1_JBCZZH.ZF_ENT_BASE_INFO ---企业基本信息165万
select * from QYGW_USER.TABLE_QYGW_QYTZXX ---企业投资信息表  1248
select * from M2_REPORT.VIEW_TAB_FILE_INDUSTRY_PRODUCT ---监督检查企业产品信息表 27534
select * from M2_PRODUCT.CP_JBXX ---产品基本信息 21955
select * from QYGW_USER.TABLE_QYGW_QYCPXX ---企业产品信息关联表 7379
select * from QYGW_USER.TABLE_QYGW_QYBZRZZZRYXX ---企业标准/认证/资质/荣誉信息关联表  1419
select * from CVICSE.TABLE_QYGW_QYFZJGJBXX ---企业分支机构基本信息关联表   20
select * from QYGW_USER.TABLE_QYGW_QYGDXX ---企业股东信息  426
select * from QYGW_USER.TABLE_QYGW_QYGGXX ---企业高管信息   699
select * from QYGW_USER.TABLE_QYGW_QYGWJBXX ---企业官网信息关联表  672
select * from M2_SPE.SE_SEBASEINFO ---特种设备基本信息1162098
select * from M2_PERSON.SE_BUSYWORKCIRCS ---特种设备作业人员情况 57684
select * from M2_PERSON.SE_SPECIALCHECKER ---特种设备作业人员信息 2873
select * from M2_PERSON.RY_JBXX ---人员基本信息表 702573 tab_file_industry_product
select * from M1_SHENPI.VIEW_TAB_FILE_INDUSTRY_PERSON ---工业产品企业人员档案表48548
select * from M2_ZZJG.JDJC_TAB_FILE_INDUSTRY ---工业产品企业信息表
select * from M1_JDJC.TAB_FILE_INDUSTRY_PRODUCT ---工业产品信息表
```

##### 导入脚本

有了上述数据之后，就可以把数据导入到Neo4j中。

以导入企业信息为例：

``` SQL
LOAD CSV WITH HEADERS FROM "file:///imp/equip.txt" AS row
CREATE (:Equip {
equipId:row.SE_ID,
equipCode:row.SE_CODE,
equipName:row.SE_NAME,
equipModel:row.MODEL,
equipUseCompanyName:row.USE_UNITS_NAME,
equipUseCompanyJgdm:row.USE_UNITS_ID,
equipMakeDate:row.MAKE_DATE,
equipBuyDate:row.BUY_DATE,
equipJjUnitsName:row.JJ_UNITS_ID,
equipJjUnitsName:row.JJ_UNITS_NAME,
equipMakeUnitsId:row.MAKE_UNITS_ID,
equipMakeUnitsName:row.MAKE_UNITS_NAME,
equipFactoryNum:row.FACTORY_NUM,
equipMakeCerName:row.MAKE_CER_NAME,
equipMakeCerNum:row.MAKE_CER_NUM
});
```

在导入了节点之后，还需要加上相应的关系，例如加上设备关系：

``` SQL
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///imp/equip.csv" AS row
MATCH (com:Company {companyJgdm:row.USE_UNITS_ID})
MATCH (equip:Equip {equipUseCompanyJgdm:row.USE_UNITS_ID})
MERGE (com)-[pu:特种设备]->(equip);
```

导入过程中出现的问题：数据中有`"\"`这样的数据，用LOAD CSV导入时会出错，可以改成`"\\"`。

导入边很慢：

#### Cypher查询

#### Spring Data Neo4j

#### 基于React的Neo4j前端

### 开发实现与实验

### 项目小结

### 工作过程

#### 第一天

确定模型（有哪些点和边），本地和服务器端搭建环境熟悉Neo4j，准备数据（从关系型数据库中导出数据），导了部分数据（Cypher导入）

![](/images/graph/work1.jpeg)
![](/images/graph/work2.jpeg)
