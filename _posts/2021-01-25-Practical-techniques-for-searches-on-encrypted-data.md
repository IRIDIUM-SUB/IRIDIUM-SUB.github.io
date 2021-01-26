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

#### 创建密文

1. 分词
2. 使用确定加密算法加密
3. 加密的单词分成两部分<L,R>
4. 生成**伪随机值**（要素察觉）
5. 伪随机函数计算密钥k=f(L)
6. 使用键控哈希对随机值进行哈希，密钥（或称键控参数）为上面生成的k
7. 得到Y=<S,F(S)>
8. Y和加密后的X亦或（Embed）

![https://pic1.zhimg.com/80/v2-2b624b795f6e6214c39a9ec76b28b2b4_720w.jpg](Practical%20techniques%20for%20searches%20on%20encrypted%20data.assets/v2-2b624b795f6e6214c39a9ec76b28b2b4_720w.jpg)

#### 生成陷门

顺序查询，如果对于最终密文C，有C XOR X 的结果为<s,F(s)>，则找到关键字。

![](Practical%20techniques%20for%20searches%20on%20encrypted%20data.assets/v2-23fde4dbe622b6cae18d25494a44310a_720w.jpg)

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