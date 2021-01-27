---
layout: post
title: Practical techniques for searches on encrypted data，Song 2000
summary: Notes for the most famous solution, Song 2000, using SWP
featured-img: moon-palace-7680x4320-kankun-mexico-best-hotels-of-2017-best-beaches-3177
---
# Practical techniques for searches on encrypted data

## Abstract

解决的问题：在不可信服务器中存储的加密文件中查询关键词，同时不损失机密性。

本文提出的方案具有**可证明安全性**

1. 为加密提供了可证明的机密性，从某种意义上讲，对不受信任的服务器仅给出密文，它就无法或难以学习到有关明文的任何信息；
2. 为搜索提供查询隔离，这意味着不受信任的服务器除了搜索结果之外，无法了解到更多有关纯文本的信息；
3. 提供受控的搜索，因此不受信任的服务器无法在未经用户授权的情况下搜索任意单词；
4. 支持隐藏查询，因此用户可以要求不受信任的服务器搜索私密的单词，而无需向服务器透露该单词；

## SWP Model


Song 等人于 2000 年提出了第一个实用的可搜索加密方案 SWP。该方案通过使用特殊的两层加密结构来搜索加密数据，该结构允许使用顺序扫描来搜索密文。核心的想法是分别加密每个单词，然后将一个哈希值（具有特殊格式）嵌入密文中。为了进行搜索，服务器可以提取该哈希值并检查该值是否具有这种特殊格式（表示查询与加密数据的匹配）。

### Exp

1. 划分加密每个单词
2. 将单词的哈希嵌入密文
3. 检索的时候检索密文的哈希，匹配则返回

### 实现细节
#### Preparation
- 用户有随机密钥k',k''
- 还需要一个Seed用于产生随机流S。
- f(x,k),F(x,k)分别为两种键控哈希函数。
- 用户有文件C，分为每个定长词w

#### 创建密文
对于每个分词W：
1. 使用密钥k''和对称加密算法加密w->X
2. 分成L,R两个部分
3. 使用Seed生成随机流S(这里应该是每个文件生成一个),这里使用的是Si
4. 生成密钥:ki=f(L,k')
5. Y=(Si,F(Si,ki))
6. C=Xi XOR Yi
7. Ci作为该单词的密文发送给服务器
#### 搜索:本地部分(创建陷门)
1. 使用密钥k''和对称加密算法加密关键词w->X
2. X=<L,R>
3. k=f(L,k')
4. Send(X,k)
#### 搜索:服务器部分(检索)
对于每个文件C的每个词W:
1. (S,P)=W XOR X
2. 检验P,如果:
    1. P=F(S,k),则匹配,返回文件内容(?)C和关键词位置index
    2. 如果不匹配,检查下一个
3. Failed
#### 解密
对每一个密文:
1. C=<Cl,Cr>
2. 找到对应的Seed,生成加密时一样的随机流Si
3. Xl=Cl XOR Si
4. K=f(Xl,k')
4. Tp=(Si,F(Si,K))
5. Xp=Cp XOR Tp
6. Wp=Decrypt(Xp),这里是之前的对称算法的解密操作,使用k''作为密钥.

### Flow chart
[![szsn5n.png](https://s3.ax1x.com/2021/01/27/szsn5n.png)](https://imgchr.com/i/szsn5n)
## Effectiveness Analysis

搜索时线性遍历，每个词需要XOR和伪随机函数各一个。

加密时为每个单词划分一个加密，一个XOR和两个伪随机

生成陷门时需要加密和伪随机函数。

## Security Analysis

IND-CPA安全

查询会泄露关键字的潜在位置，查询几次后可以通过统计分析学习文档中的内容。

## Limitations

- 只能使用固定大小的单词单元
- 仅限plain text类别 不适用于压缩文件

## Reference

https://zhuanlan.zhihu.com/p/135382555