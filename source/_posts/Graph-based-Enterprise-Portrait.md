---
title: 基于图数据库的企业画像
date: 2018-06-21 12:39:26
tags: 项目纪要
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
导入企业（120w数据）与设备(116w)关系时非常慢，改用**neo4j-import**进行导入
```bash
./bin/neo4j-import --into ./data/graph3.db --multiline-fields true --ignore-missing-nodes true --skip-duplicate-nodes true \
 --bad-tolerance 1000000 --ignore-empty-strings true --ignore-extra-columns true \
--nodes:Company ./import/imp/company_header.csv,./import/imp/company.csv \
--nodes:Product ./import/imp/product_header.csv,./import/imp/product.csv \
--nodes:Equip ./import/imp/equip_header.csv,./import/imp/equip.csv \
--relationships ./import/imp/r_equip_company_header.csv,./import/imp/r_equip_company.csv \
--nodes:Employer ./import/imp/employer_header.csv,./import/imp/employer.csv \
--relationships ./import/imp/r_employer_company_header.csv,./import/imp/r_employer_company.csv \
--nodes:Product ./import/imp/product_header.csv,./import/imp/product.csv \
--relationships ./import/imp/r_product_company_header.csv,./import/imp/r_product_company.csv
```

**注意事项：**
导入过程如果报错

```bash
Input error: ''
Caused by:''
java.lang.IllegalArgumentException: ''
	at org.neo4j.csv.reader.Extractors.valueOf(Extractors.java:155)
	at org.neo4j.unsafe.impl.batchimport.input.csv.DataFactories$DefaultRelationshipFileHeaderParser.entry(DataFactories.java:365)
	at org.neo4j.unsafe.impl.batchimport.input.csv.DataFactories$AbstractDefaultFileHeaderParser.create(DataFactories.java:175)
	at org.neo4j.unsafe.impl.batchimport.input.csv.InputGroupsDeserializer.createNestedIterator(InputGroupsDeserializer.java:94)
	at org.neo4j.unsafe.impl.batchimport.input.csv.InputGroupsDeserializer.createNestedIterator(InputGroupsDeserializer.java:38)
	at org.neo4j.helpers.collection.NestingIterator.fetchNextOrNull(NestingIterator.java:68)
	at org.neo4j.helpers.collection.PrefetchingIterator.peek(PrefetchingIterator.java:60)
	at org.neo4j.helpers.collection.PrefetchingIterator.hasNext(PrefetchingIterator.java:46)
	at org.neo4j.unsafe.impl.batchimport.staging.IteratorBatcherStep.nextBatchOrNull(IteratorBatcherStep.java:63)
	at org.neo4j.unsafe.impl.batchimport.InputIteratorBatcherStep.nextBatchOrNull(InputIteratorBatcherStep.java:46)
	at org.neo4j.unsafe.impl.batchimport.staging.PullingProducerStep.process(PullingProducerStep.java:43)
	at org.neo4j.unsafe.impl.batchimport.staging.ProducerStep$1.run(ProducerStep.java:61)
```

请检查header文件格式是否写错

#### Cypher查询

**简单查询对象**

``` SQL
MATCH(com:Company)
WHERE com.companyName='××股份有限公司'
RETURN p1
```

**查询对象并查询该企业下的职工**

``` SQL
MATCH(com:Company)
WHERE com.companyName='××股份有限公司'
optional match p1=(com)-[r1:职工]->(emp:Employer)
RETURN p1
```

**建立自然人投资关系**

``` SQL
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///r_investor_company.csv" AS row
MATCH (emp:Employer {employerName:row.investor})
MATCH (com2:Company {ENT_ID:row.companyId})
MERGE (emp)-[pu:自然人股东]->(com2);
```

**建立企业法人投资关系**

``` SQL
load csv with headers from "file:///imp/legal_company.csv" as line
match (emp:Employer{ZJHM: line.FDDBRHM})
match (com:Company{companyJgdm: line.ZZJGDM})
merge (emp) - [:企业法人] -> (com)
```

**查询XX公司1～6层的企业股东**

``` SQL
Match (start:Company{companyName:'XX公司'})-[:企业股东*1..6]-(end:Company) 
return end
```

#### Spring Data Neo4j

#### 基于React的Neo4j前端

### 开发实现与实验

### 项目小结

### 工作过程

#### 第一天

确定模型（有哪些点和边），本地和服务器端搭建环境熟悉Neo4j，准备数据（从关系型数据库中导出数据），导了部分数据（Cypher导入）

![](/images/graph/work1.jpeg)
![](/images/graph/work2.jpeg)

#### 第二天

使用Cypher导入数据后，建立点与边之间的关系，遇到性能问题，改用**neo4j-import**进行导入，解决建立关系慢的问题。导入数据后建立领用Cypher查询简单图谱
![](/images/graph/work21.jpg)

#### 第三天
梳理企业投资关系数据，构建企业投资关系；学习Cypher，利用Cypher查询企业投资关系图谱
![](/images/graph/companyRelationShips.png)

学习Spring Data Neo4j，领用Java语言进行简单的Cypher操作