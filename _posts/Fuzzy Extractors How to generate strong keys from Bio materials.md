---
layout: post
title: Fuzzy Extractors：How to generate strong keys from Bio materials
summary: Notes for Graduation Project, about how to generate error tolerated keys via bio meterials.
featured-img: takashi-watanabe-f2DL8oI-7N8-unsplash
---
# Fuzzy Extractors: How to generate strong keys from Bio materials

[TOC]

## 生物特征的特点

1. 不能精确再现。每次再现会带有一定的不可预测的随机误差
2. 不能均匀分布（？）->随机性强

## 随机串**作为密钥**在密码中的作用

对于密钥w，当且仅当f(w)=y，验证成功后才放行。为保证不可破解，f(x)应该难以反求比如MD5，即知道y难以求密钥w。

存在的问题：上述方案中w是不能变的，因此不能适配生物识别方案。如果通过记忆密码，w的长度不能很长因此难以保证安全性。即安全性（足够长，密钥空间足够大）和准确性（w唯一）的冲突。



## 两个基本概念

### Secure Sketch

一个概率函数，特点是密钥w及附近的值的输出均为公共值v，同时是一个单向函数，不能从v回推w。

> The price for this error tolerance is that the application will have to work with a lower level of entropy of the input, since publishing v effectively reduces the entropy of w. However, in a good secure sketch, this reduction will be small, and w will still have enough entropy to be useful, even if the adversary knows v. A secure sketch, however, does not address non-uniformity of inputs.



代价：

1. 更低的输入熵
2. 但在优秀的设计下，这种代价是很轻微的，并且生物特征有足够的随机性（熵），即使在对手知道v的情况下。
3. 但设计并不能抵抗输入的不均匀性，即需要结合生物特征的随机性保证安全。



### Fuzzy Extractor

可以从生物特征输入w中可靠的提取一个均匀随机的字符串r。如果输入轻微变化，输出不变。

How it works:

> For a concrete example of how to use fuzzy extractors, in the password authentication case, the server can store <P, f(R)>. When the user inputs w close to w, the server recovers the actual R and checks if f(R) matches what it stores. Similarly, R can be used for symmetric encryption, for generating a public-secret key pair, or any other application. Secure sketches and extractors can thus be viewed as providing fuzzy key storage: they allow recovery of the secret key (w or R) from a faulty reading w' of the password w, by using some public information (v or P). In particular, fuzzy extractors can be viewed as error- and nonuniformity-tolerant secret key key-encapsulation mechanisms

服务器储存公开串P和单向函数结果f(R)，其中R是使用生物特征认证之后恢复出的真正密钥。当f(R)校验通过后校验通过。

当输入w附近的特征之后，恢复出R并校验。

R可用于对称加密，生成公共秘密密钥对或任何其他应用。

Secure Sketch和Extractor允许通过使用一些公共信息（v或P）从密码w的不准确读取w'中恢复秘密密钥（w或R）。特别地，模糊提取器可以被视为容错和非均匀性的秘密密钥封装机制

## 可行性

文章证明了模糊提取器可以利用强随机性提取器构建安全草图，并且因此可得这些存在安全草图的空间存在纠错码以修正结果。

## 生物特征的偏移形式

> Because different biometric information has different error patterns, we do not assume any particular notion of closeness between w and w. Rather, in defining our primitives, we simply assume that w comes from some metric space, and that w is no more that a certain distance from w in that space. We only consider particular metrics when building concrete constructions.

由于不同生物特征之间的差异不同，所以仅仅度量w'和w的空间距离不超过一定阈值。

距离由三个指标定义：

### Hamming Distance

相对naive的方式，即度量w'和w之间不同位的数量。

### Set Difference

w'和w对称差的大小，数学上，两个集合的对称差是**只属于其中一个集合，而不属于另一个集合的元素**组成的集合。 集合论中的这个运算相当于布尔逻辑中的异或运算。

> The set difference metric (i.e., size of the symmetric difference of two input sets w and w′) comes up naturally whenever the biometric input is representedas a subset of features from a universe of possible features.

**生物特征表示为所有可能特征的子集的时候**

> We demonstrate the existence of optimal (with respect to entropy loss) secure sketches (and therefore also fuzzy extractors) for this metric. However, this result is mainly of theoretical interest, because 
>
> (1) it relies on optimal constant-weight codes, which we do notknow how construct and 
>
> (2) it produces sketches of length proportional to the universe size. 
>
> We then turn our attention to more efficient constructions for thismetric, and provide two of them.

存在的问题：

1. 不明确构造*optimal constant-weight codes*最优恒权码
2. Sec sketch长度和全部可能的空间大小成正比。

### Edit Distance

> First, we observe that the “fuzzy vault” construction of Juels and Sudan can be viewed as a secure sketch in thismetric (and then converted to a fuzzy extractor using our general result). We provide a new, simpler analysis for this construction, which bounds the entropy lost from w given v. Our bound on theloss is quite high unless one makes the size of the output v very large. We then provide an improvement to the Juels-Sudan construction to reduce the entropyloss to near optimal, while keeping v short (essentially as long as w)

基于*fuzzy vault*

更简单的分析方法

限制了从w丢失的熵

减少熵的损失，保证了v足够短

> Second, we note that in the case of a small universe, a set can be simply encoded as its characteristic vector (1 if an element is in the set, 0 if it is not), and set difference becomes Hamming distance. However, the length of such a vector becomes unmanageable as the universe size grows. Nonetheless, we demonstrate that this approach can be made to work efficiently even for exponentially large universes. This involves a result that may be of independent interest: we show that BCH codes can be decoded in time polynomial in the weight of the received corrupted word (i.e., in sublinear time if the weight is small). The resulting secure sketch scheme compares favorably to the modified Juels-Sudan construction: it has the same near-optimal entropy loss, while the public output v is even shorter (proportional to the number of errors tolerated, rather than the input length).

其次，我们注意到，在universe较小的情况下，集合可以简单地编码为其特征向量(如果元素在集合中，则为1，如果元素不在集合中，则为0)，集合差成为汉明距离。然而，随着universe大小的增长，这样一个矢量的长度变得难以控制。尽管如此，我们证明了这种方法**即使在指数大universe中也能有效地工作**。这涉及到一个可能独立的结果:**我们证明了BCH码（Ref：[BCH码 - 维基百科，自由的百科全书 (wikipedia.org)](https://zh.wikipedia.org/zh-hans/BCH码)）可以在接收到的被损坏字的权值内以时间多项式的形式解码**(即，如果权值较小，则以次线性时间解码)。与改进后的Juels-Sudan结构相比，安全的sketch方案具有相同的接近最优的熵损失，而公共输出v更短(与允许的错误数成比例，而不是与输入长度成比例)。

> Finally, edit distance (i.e., the number of insertions and deletions needed to convert one string into the other) naturally comes up, for example, when the password is entered as a string, due to typing errors or mistakes made in handwriting recognition. We construct a biometric embedding from the edit metric into the set difference metric, and then apply our general result to show such an embedding yields a fuzzy extractor for edit distance, because we already have fuzzy extractors for set difference. We note that the edit metric is quite difficult to work with, and the existence of such an embedding is not a priori obvious: for example, low-distortion embeddings of the edit distance into the Hamming distance are unknown and seem hard . It is the particular properties of biometric embeddings, as we define them, that help us construct this embedding.

最后，编辑距离(**即将一个字符串转换成另一个字符串所需的插入和删除数量**)自然出现，例如，当密码作为字符串输入时，由于键入错误或手写识别错误。我们从编辑度量构造一个生物特征嵌入到集差度量中，然后应用我们的一般结果来显示这样的嵌入产生编辑距离的模糊提取器，因为我们已经有用于集差的模糊提取器。我们注意到编辑度量是相当难以处理的，而且这种嵌入的存在并不是先天明显的:例如，低失真的编辑距离嵌入到汉明距离是未知的，看起来很难。正是我们定义的生物特征嵌入的特殊特性，帮助我们构建了这种嵌入。 

## Realted Works

### 不统一和低熵密码的处理

> The need to deal with nonuniform and low-entropy passwords has long been realized in the security community, and many approaches have been proposed. For example, Ellison et al. [10] propose asking the user a series of n personalized questions, and use these answers to encrypt the “actual” truly random secret R. A similar approach using user’s keyboard dynamics (and, subsequently, voice [21,22]) was proposed by Monrose et al [20]. Of course, this technique reduces the question to that of designing a secure “fuzzy encryption”. While heuristic approaches were suggested in the above works (using various forms of Shamir’s secret sharing), no formal analysis was given. Additionally, error tolerance was addressed only by brute force search.

提出的方案：

1. 使用个性化问题生成的串来加密秘密随机串
2. 键盘动力学（？？）

## Error tolerance

> A formal approach to error tolerance in biometrics was taken by Juels and Wattenberg [15] (for less formal solutions, see [8,20,10]), who provided a simple way to tolerate errors in uniformly distributed passwords. Frykholm and Juels [12] extended this solution; our analysis is quite similar to theirs in the Hamming distance case. Almost the same construction appeared implicitly in earlier, seemingly unrelated, literature on information reconciliation and privacy amplification (see, e.g., [3,4,7]). We discuss the connections between these works and our work further in Section 4.

生物统计学的容错方法（？）

