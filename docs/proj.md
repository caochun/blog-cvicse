# 项目说明

## 介绍

现有一批质监局数据，导出自某数据库，包含的基本关系有：

``` c
企业基本信息165万
企业投资信息表  1248
监督检查企业产品信息表 27534
产品基本信息 21955
企业产品信息关联表 7379
企业标准/认证/资质/荣誉信息关联表  1419
企业分支机构基本信息关联表   20
企业股东信息  426
企业高管信息   699
企业官网信息关联表  672
特种设备基本信息 1162098
特种设备作业人员情况 57684
特种设备作业人员信息 2873
人员基本信息表 702573
```

根据以上主要关系，可以构建包含企业、产品、法人、投资关系等的图模型，如下所示。主要包括，企业所生产的产品、企业股东、法人等。
![rel](pics/rel2.png)

背景：质监局会对企业产品进行质量检测，反馈合格或者不合格，另外还有对整个公司的检测结果（这里并不确定是怎么来的。

任务：根据已有部分抽检产品信息和检测结果，根据他们之间的关系，包括产品所属公司，公司之间的投资关系，人员关系等，构建模型，对所有未检测产品进行分类，预测它们可能的检测结果。

任务特征：

- 节点具有多种类型，包括产品、公司、人等；
- 产品的相似度可能跟他们所属的公司有关，与它们所属的产品类型有关等；
- 单从画的几个节点类型和关系类型，整个图是否完全连同，或者有太多分枝，每个都属于小的子图。

任务抽象：

- 属于半监督的异质图上的节点分类问题。

## 现有工作和算法

### metapath2vector

Dong Y, Chawla N V, Swami A. metapath2vec: Scalable representation learning for heterogeneous networks[C]//Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining. ACM, 2017: 135-144.

该工作是对异质网络（即包含多种类型的节点和边的网络）中的每个节点学习生成较低维度空间的嵌入表达（embedding）。根据得到的嵌入表达，可以进行基本的分类、聚类等任务。

不符合之处：

- 该工作属于无监督学习，没有考虑已有的节点信息，即在生成每个节点的低维嵌入时没有考虑已有的分类信息。

### Semi-supervised classification with graph convolutional networks

Kipf T N, Welling M. Semi-supervised classification with graph convolutional networks[J]. arXiv preprint arXiv:1609.02907, 2016.

改工作是在普通同质图（相对前面异质图而言，即没有各种节点或者边的类型，只有每个点之间相互连接的信息）上使用GCN（graph convolutional networks）方法做的半监督分类问题。

不符合之处：

- 该工作是对普通图做的半监督学习，如果直接使用则会丢弃很多已有信息。

### Inductive Representation Learning on Large Graphs

Hamilton W, Ying Z, Leskovec J. Inductive representation learning on large graphs[C]//Advances in Neural Information Processing Systems. 2017: 1024-1034.

这份工作还没细看，有待研究，另外结合前两篇的工作，可以完成本次要做的任务。

## 方法

姚老师给出了两种解决方法：

1. 先使用metapath2vector得到已有异质图的低维嵌入表达，然后根据该embedding重构生成同质图，之后就可以使用GCN的方法进行半监督的分类学习。这个方法较为简单，但可能重构出的新图会损失部分信息，新图的结构还在考虑。

2. 结合两份工作，将metapath2vector的方法结合进GCN中，但怎么结合我还不明白，有关GCN的具体方法、优化目标还在研究之中。这种方法可能需要修改现有的部分源代码，工作量可能较大。

## 计划

1. 需要将已有数据进行过滤，去掉关系中不需要的属性；并转化成图的数据结构。
2. 继续研究GCN的方法，尝试进行结合。
3. 跑一些metapath2vector和GCN的实验。