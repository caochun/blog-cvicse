---
title: 监督检查、抽查数据说明文档
date: 2018-06-26 14:39:35
tags: 项目纪要
description: 监督检查、抽查数据说明文档
---
## 数据存储位置

```
/home/nju/neo4j/import/imp_new/check.csv
```

## 字段说明

| 字段名  | 说明 |
| ------------- | ------------- |
| ID | 监督检查表主键 |
| PLANTYPE | 计划类型,分为国家级监督抽查、省级监督抽查、市级监督抽查、县级监督抽查、其它监督抽查、省级专项监督抽查、市级专项监督抽查、定期监督抽查、定检 |
| VERIFYORG | 承检机构 |
| ORGNAME | 企业名称 |
| PRODADDR | 生产地址 |
| REGION | 行政区划内容 |
| SCALECONTENT | 企业规模描述 |
| ECONOMYTYPECONTENT | 经济类型内容 |
| CONMAN | 联系人 |
| CONTEL | 联系电话 |
| SINGLEMAN | 法人代表 |
| ORGANNO | 组织机构代码 |
| PRODSTATENAME | 现场生产状态文字 |
| ISREFUSE | 企业是否拒检1是0否 |
| PRODNAME | 产品具体名称 |
| MODEL | 规格型号 |
| PRODUCTTYPE | 产品类别 |
| STANDARDNAME | 检验依据标准(标准编号和名称) |
| PTIDS | 标准产品分类 |
| BATCHNUM | 生产日期或批号 |
| SALESVOLUME | 销售额（万元） |
| WORKERNUM | 人数 |
| YEARTOTALOUTPUT | 产值（万元） |
| PRODAMOUNT | 产量 |
| REPORTNO | 报告编号 |
| ENTITYISPASS | 实物质量判定(1合格0不合格) |
| LABELISPASS | 标签判定(1合格0不合格) |
| ISPASS | 综合判定(1合格0不合格) |
| REMARK | 备注 |
| STATE | 报告状态(0编辑中、1提交2退回) |
| RECHECKORG | 复查检验机构名称 |
| RECHECKORGCODE | 后处理单位行政区划号 |
| ISRECHECK | 是否已整改 |
| ISPUNISH | 是否已处罚 |
| ISTEST | 是否移送检验 |
| AFTERDATE | 后处理截止时间 |
| RECHECKDATE | 整改要求完成时间 |
| POST | 邮政编码 |
| BUSLICENO | 营业执照注册号 |
| CERTNO | 许可证编号 |
| CCCCERTNO | CCC编号 |
| ISCANCEL | 是否作废（1作废，0不作废） |
| ISFUJIAN | 是否复检（1是，0否） |
| PLANCLASS | 计划类别 1监督检查,2监督检验 |
| PLANMAKORG | 计划编制单位 |
| ZXSTANDARDCODE | 执行标准编号 |
| ZXSTANDARDNAME | 执行标准名称 |
| ISHISTORY | 是否历史数据(1是0否) |
| SUBTIME | 提交时间 |
| ISIMPORTANT | 是否严重不合格 |
| RZXCODE | 认证项 |
| QYRYCODE | 企业荣誉 |
| CYLYCODE | 抽样领域 |
| RELATIONID | 与复检相关连的报告 |
| PTIDSNAME | 标准产品分类名称 |
| ADDR | 单位地址 |
| PLANNAME | 计划编号 |
| JYXS | 检验项数 |
| UNPASSXS | 不合格项数 |
| UNPASSXM | 不合格项目 |
| CHECKOKDATE | 实际整改完成时间 |
| ZGFCDATE | 责令整改通知书发出日期 |
| ZGTJDATE | 企业提交整改报告日期 |
| RECHECKSQDATE | 复查申请日期 |
| RECHECKISPASS | 复查是否合格 |
| LOGREMARK | 后处理移送的备注，然后存入流转历史表的remark |
| CYDATE | 抽样日期 |
| ZGREMARK | 整改信息的备注 |
| WFHCL | 无法进行后处理 |
| RECEIVEDATE | 接收移送时间 |
| ISYUETAN | 是否约谈 |
| ISLURU | 是否人工录入的记录 |
| GRADE | 等级 |
| SAMPLING_POINT | 标准分类 |
| SOURCEDEPT | 来源部门 |
| BATCHNO | 检验批号 |
| XIANGXIID | 国抽表产品分类代码 |
| GCEJID | 一级产品分类代码 |
| GCYJID | 二级产品分类代码 |
| RETURNOPINION | 退回意见 |
| MANNAME | 处理人 |
| BRAND | 商标 |
| BACKREASON | 建议，原因 |
| BACKMANNAME | 处理人 |
| RZRKQK | 认证认可情况 |
| ZXBZQK | 执行标准/技术文件情况 |

## 企业数据说明

### 存储位置

```
/home/nju/neo4j/import/imp_new/company.csv
```
# 字段说明

| 字段      | 说明    |  
| --------   | :-----:  | 
|ZZJGDM      	|机构代码|  
|DWMC    	|单位名称  |
|JGLX      	|机构类型（暂未找到对应字典）      |  	  
|FDDBR      	|法定代表人      |
|ZJLX      	|证件类型（身份证号）      |
|FDDBRHM      	|法定代表人证件号码      |
|JYFW      	|经营范围      |
|HY      	|行业名称     |
|JJLX      	|经济类型     |
|SHRQ      	|审核日期      |
|ZGDM      	|主管部门代码      |
|PZJGMC     |批准机构名称      |
|XZQH      	|行政区划      |
|ZCDZ      	|注册地址      |
|YZBM      	|邮政编码     |
|DH      	|电话号码      |
|SSZB      	|实收资本      |
|BZRQ      	|办证日期      |
|ZFRQ      	|作废日期     |
|BZJG      	|办证机构      |
|ZYCP      	|主要产品      |
|JBR      	|经办人      |
|CK      	|是否出口      |
|ZCZJ      	|注册资金      |
|HBZL      	|货币种类      |
|WFTZGB     |投资国别或地区代码     |	  
|ZGRS      	|职工人数      |
|CLRQ      	|成立日期      |
|ZYGD      	|主要股东      |
|PZWH      	|批准文号      |
|BGRQ      	|变更日期      |
|JYYZBM      	|经营邮政编码      |
|NJRQ      	|年检日期      |
|GK      	|是否公开      |
|NJQX      	|年检期限      |
|EMAIL      	|邮箱      |
|WZ      	|网址      |
|ZCH      	|注册号      |
 |YDDH      	|移动电话      |
|XJJHY      	|新经济行业      |
|ZGBM      	|主管部门名称      |
|DJPZJG      	|登记批准机构代码      |
|JYDZ      	|经营地址      |

## 产品数据说明

### 存储位置

```
/home/nju/neo4j/import/imp_new/product.csv
```
# 字段说明

| 字段      | 说明    |  
| --------   | :-----:  | 
|  ZZJGDM | 组织机构代码 |
|ORGAN_NM | 企业名称|
|ECONOMY_TYPE |经济类型1.国有 2.集体 3.私营 4.个体 5.联营 6.股份制 7.外商投资 8.港澳台投资 9.其他|
|FILLTIME | 申请日期 |
|PROD_NM | 产品名称 |
|PROD_MODEL_CH | 产品型号 |
|STAND_NO | 执行标准编号 |
|STAND_NM | 执行标准名称 |
|CERTNUM | 等级证书编号（证书副本编号）|
|INFILEDATE | 归档日期 |

## 人员数据说明

### 存储位置

```
/home/nju/neo4j/import/imp_new/employee.csv
```
# 字段说明

| 字段      | 说明    |  
| --------   | :-----:  | 
|ZJHM | 证件号码|
|NAME | 姓名 |
|SEX | 性别 |
|BIRTHDAY | 出生日期 |
|ZY | 所学专业 |
|XL | 学历 |
|XW | 学位 |
|ZC | 职称 |
|ZJLX | 证件类型 |
|NATION | 民族 |
|ORGAN_ID | 所属组织机构代码 |
|TXDZ | 通讯地址 |
|YB | 邮编 |
|LXFS | 联系方式 |
|ORGAN_NAME | 组织机构名称 |

## 特种设备数据说明

### 存储位置

```
/home/nju/neo4j/import/imp_new/equip.csv
```
# 字段说明


| 字段      | 说明    |  
| --------   | :-----:  | 
|ID|主键|
|CHUCHANGBIANHAO|出厂编号|
|DENGJIZHENGBIANHAO|登记证编号|
|NEIBUBIANHAO|内部编号|
|SHEBEIDAIMA|设备代码|
|SHEBEIDIDIAN|设备地点|
|SHEBEILEIBIE|设备类别|
|SHEBEIMINGCHENG|设备名称|
|SHEBEIXINGHAO|设备型号|
|SHEBEIZHONGLEI|设备种类|
|SHIGONGDANWEI|施工单位|
|SHIGONGDANWEIID|施工单位ID|
|SHIGONGDANWEI_DIZHI|施工单位地址|
|SHIGONGDANWEI_LIANXIDIANHUA|施工单位联系电话|
|SHIGONGDANWEI_LIANXIREN|施工单位联系人|
|SHIYONGDANWEI|使用单位|
|SHIYONGDANWEIID|使用单位ID|
|SHIYONGDANWEI_DIZHI|使用单位地址|
|SHIYONGDANWEI_LIANXIDIANHUA|使用单位联系电话|
|SHIYONGDANWEI_LIANXIREN|使用单位联系人|
|STATE|状态|
|ZHIZAORIQI|制造日期|
|ZHUCEDAIMA|注册代码|
|ZHUCERIQI|注册日期|
|JIANYANJIESHURIQI|检验结束日期|
|JIANYANKAISHIRIQI|检验开始日期|
|QUHUADAIMA|区划代码|
|QUHUAMINGCHENG|区划名称|
|SHIGONGDANWEI_JIANCHENG|施工单位简称|
|SHIGONGDANWEI_XUKEZHENGBIANHAO|施工的单位许可证编号|
|SHIYONGDANWEI_CODE|使用单位代码|
|WEIHUBAOYANGDANWEI|维护保养单位|
|WEIHUBAOYANGDANWEI_ID|维护保养单位ID|
|XIACIJIANYANRIQI|下次检验日期|
|ZHIZAODANWEI|制造单位|
|DTYPE|电梯型式|
|BIAOZHUNJIAGE|标准价格|
|DINGSHENGXINGSHI|顶升型式|
|EDINGSUDU|额定速度 |
|EDINGSUDU_SHANGXING|额定速度上行|
|EDINGSUDU_XIAXING|额定速度下行|
|EDINGZAIZHONGLIANG|额定载重量|
|ERLIEJIANYANHUANJING|恶劣检验环境|
|FUJIAN|复检|
|GONGGONGJIAOTONGXING|公共交通型|
|JIAOYANXIANSUQI|校验限速器|
|KONGZHIFANGSHI|控制方式|
|MINGYIKUANDU|名义宽度|
|MINGYISUDU|名义速度|
|QINGXIEJIAO|倾斜角|
|QUDUANCHANGDU|区段长度|
|TISHENGGAODU|提升高度|
|YOUGUANSHULIANG|油罐数量|
|ZCSTATUS|注册状态|
|CAIZHI|材质|
|CHELIANGDIPANHAO|车辆底盘号|
|CHELIANGLEIXING|车辆类型|
|CHELIANGXINGHAO|车辆型号|
|CHEPAIHAO|车牌号|
|CHEZHU|车主|
|FADONGJIHAO|发动机号|
|GONGZUOJIEZHI|工作介质|
|LIANXIDIANHUA|联系电话|
|QIPINGJINGZHONG|气瓶净重|
|QIPINGSHULIANG|气瓶数量|
|QITILEIXING|气体类型|
|RONGJI|容积|
|TONGXUNDIZHI|通讯地址|
|XIANWEI|纤维|
|ZHENGBEIZHONGLIANG|整备重量|
|ZHONGLIANG|重量|
|FAZHENGRIQI|发证日期|
|SFSHUODING|是否锁定|
|SHEBEILEIBIEDAIMA|设备类别代码|
|SHEBEIZHONGLEIDAIMA|设备种类代码|
|SHENBEIPINZHONGDAIMA|设备品种代码|
|WEIHUBAOYANGDANWEI_DIANHUA|维护保养单位电话|
|GAOZHIRIQI|告知日期|
|GAOZHIBIANHAO|告知编号|
|ANZHUANGRIQI|安装日期|
|ZHIZAODANWEIXUKEZHENGBIANHAO|制造单位许可证编号|
|SHEBEIPINZHONG|设备品种|
|SZSTATUS|是否市直|
|SHEBEIXINGSHI|设备型式|
|SHIGONGLEIBIE|施工类别|
|SHIGONGLEIBIEDAIMA|施工类别代码|
|SHIYONGDANWEI_YOUZHENGBIANMA|使用单位邮政编码|
|JIANYANJIELUN|检验结论|
|SHEBEIBAOJIANRIQI|设备报检日期|
|JIANYANLEIXING|检验类型|
|SHIGONGDANWEI_XKZ_YOUXIAOQI|施工单位许可证有效期|
|SHIYONGDANWEI_CHUANZHEN|使用单位传真|
|SHIYONGDANWEI_YOUBIAN|使用单位邮编|
|GAOZHIJINGBANREN|告知经办人|
|SHIGONGDANWEI_CHUANZHEN|施工单位传真|
|SHIGONGDANWEI_YOUBIAN|施工单位邮编|
|GZ_FILEPATH|告知文件路径|
|BAOGAOBIANHAO|报告编号|
|JYBG_ID|检验报告ID|
|BJ_JIANYANXIANGMU|办结报告检验项目|
|DJ_JINGBANREN|登记经办人|
|DJ_JINGBANRIQI|登记经办日期|
|FLAG|设备发送状态|
|ZFSTATE|作废状态|
|DJB_ID|登记表ID|
|EQHIDE|是否隐患设备|
|BANJIERIQI|办结日期|
|BIAOZHULEIXING|标注类型|
|FJ_FILEPATH|附件|
|TMPDATA|是否为临时数据|
|PID|源数据ID|
|SHI|市|
|SHIDAIMA|市代码|
|XIANGZHEN|乡镇|
|XIANGZHENDAIMA|乡镇代码|
|CHANQUANDANWEI|产权单位|
|CHANQUANDANWEI_DIZHI|产权单位地址|
|CHANQUANDANWEI_DAIMA|产权单位代码|
|JIANYANJIGOU|检验机构|
|BIANGENGRIQI|变更日期  20160427|
|ANQUANGUANLIBUMEN|安全管理部门|
|ANQUANGUANLI_LIANXIDIANHUA|安全部门联系电话|
|ANQUANGUANLIRENYUAN|安全管理人员|
|SHIYONGDANWEI_SUOSHUHANGYE|使用单位所属行业|
|CHANQUANDANWEI_SUOSHUHANGYE|产权单位所属行业|
|NJ_BAOGAOBIANHAO|内检报告编号|
|WJ_BAOGAOBIANHAO|外检报告编号|
|ND_BAOGAOBIANHAO|年度报告编号|
|QM_BAOGAOBIANHAO|全面报告编号|
|CHANQUANDANWEI_FARENDAIBIAO|产权单位法人代表|
|CHANQUANDANWEI_DIANHUA|产权单位电话|
|FADINGDAIBIAOREN|法定代表人|
|ZHUGUANFUZEREN|主管负责人|
|ZHUGUANFUZERENDIANHUA|主管负责人电话|
|JINGBANREN|经办人|
|JINGBANRENDIANHUA|经办人电话|
|GUDINGZICHANZHI|固定资产值|
|SHEBEISHIYONGNIANXIAN|设备使用年限|
|WHBYDW_ZIZHIZHENGSHU|维保单位资质证书|
|WEIHUBAOYANGDANWEI_LIANXIREN|维保单位联系人|
|FRDB_DIANHUA|使用单位法人电话|
|CQDW_FRDBDH|产权单位法人电话|
|GONGCHENGMINGCHENG|工程名称|
|ZHUGUANBUMEN|主管部门|
|HANGYE|行业|
|ZHUCEDENGJIRENYUAN|注册登记人员|
|ZHUCEDENGJIJIGOU|注册登记机构|
|SHENHERIQI|审核日期|
|DENGJIYIJIAN|登记意见|
|SHEJIDANWEI|设计单位|
|SHEJIDANWEIDAIMA|设计单位代码|
|TOUYONGRIQI|投用日期|
|SUOSHUCHEJIAN|所属车间|
|BEIZHU|备注|
|SHIGONGDANWEI_DAIMA|施工单位代码|
|ZHIZAORIQI_DATE|制造日期（日期型）|
|TOUYONGRIQI_DATE|投用日期（日期型）|
|CHANQUANDANWEI_YOUBIAN|产权单位邮政编码|
|CHANQUANDANWEI_XINGZHI|产权单位性质|
|SHIYONGDANWEI_XINGZHI|使用单位单位性质|
|ANQUANDENGJI|安全等级|
|ZHIGONGRENSHU|职工人数|
|NIANCHANZHI|年产值|
|TIANBIAORENYUAN|填表人员|
|JIANGUANLEIBIE|监管类别|
|ZHIZAODANWEI_CODE|制造单位代码|
|ZHIZAODANWEI_ZIGEZHENGSHU|制造单位资格证书|
|ZHIZAODANWEI_LIANXIDIANHUA|制造单位联系电话|
|ZHIZAODANWEI_SHIYONGCHANGHE|适用场合|
|CHANPINHEGEZHENGBIANHAO|产品合格证编号|
|JIANDINGBAOGAOBIANHAO|鉴定报告编号|
|WENJIANJIANDINGJIGOU|文件鉴定机构|
|ZHIZAOJIANJIANJIGOU|制造监检机构|
|JIANJIANZHENGSHUBIANHAO|制造监检证书编号|
|JIANJIANHEZHUNZHENG|制造监检核准证|
|ZHIZAOJIANJIANRIQI|制造监检日期|
|ANZHUANGDANWEI|安装单位|
|ANZHUANGDANWEI_DAIMA|安装单位代码|
|ANZHUANGDANWEI_ZIGEZHENGSHU|安装单位资格证书|
|ANZHUANGDANWEI_LIANXIREN|联系人|
|ANZHUANGDANWEI_LIANXIDIANHUA|联系电话|
|ANZHUANGJIANJIANRIQI|安装监检日期|
|ANZHUANGJIANJIANJIGOU|安装监检机构|
|SHIGONGDANWEI_SHIGONGRIQI|施工单位施工日期（施工告知日期）|
|SHIGONGDANWEI_JUNGONGRIQI|施工单位竣工日期|
|YANSHOUDANWEI|验收单位|
|YANSHOURIQI|验收日期|
|YANSHOUJIGOU|验收机构|
|YANSHOUBAOGAOBIANHAO|验收报告编号|
|DAXIUZHOUQI|大修周期 （月）|
|WEIBAOZHOUQI|维保周期 （周）|
|WEIHUBAOYANGDANWEI_CODE|维保代码|
|JIANYANJIGOU_CODE|检验机构代码|
|ZHUYAOWENTI|主要问题|
|SHIGULEIBIE|事故类别|
|SHIGUFASHENGRIQI|事故发生日期|
|SHIGUCHULIJIEGUO|事故处理结果|
|SHEBEIBIANDONGFANGSHI|设备变动方式|
|SHEBEIBIANDONGXIANGMU|设备变动项目|
|CHENGDANDANWEI|承担单位|
|SHEBEIBIANDONGRIQI|设备变动日期|
|CHENGDANDANWEI_CODE|承担单位代码|
|BAOXIANJIGOU|保险机构|
|BAOXIANXIANZHONG|保险险种|
|BAOXIANFEI|保险费|
|BAOXIANJI|保险金额|
|BAOXIANJIAZHI|保险价值|
|SHIFOU_ZHONGDAWEIXIANYUAN|是否重大危险源|
|SHIFOU_RENKOUMIJIQU|是否密集区|
|SHIFOU_YINGJIFANGAN|是否应急方案|
|SHIFOU_ZHONGDIANJIANKONG|是否重点监控|
|SHIYONGCHANGSUO|使用场所|
|SHIYONGDANWEI_CAOZUORENYUAN|操作人员|
|TIANBIAORIQI|填表日期|
|DENGJILEIBIE|登记类别|
|CHANPINTUHAO|产品图号|
|SHIGONGDANWEI_YOUZHENGBIANMA|施工单位邮政编码|
|SHEJIXUKEZHENG|设计许可证编号|
|SHIFOU_DYRRJS|是否吊运熔融金属|
|JINGDU|经度|
|WEIDU|纬度|
|UPDATETIME|更新日期|