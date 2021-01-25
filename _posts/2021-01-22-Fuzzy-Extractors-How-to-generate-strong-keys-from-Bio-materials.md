---
layout: post
title: Fuzzy Extractors-How to generate strong keys from Bio materials
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

> Juels and Sudan [16] provided the first construction for a metric other than Hamming: they construct a “fuzzy vault” scheme for the set difference metric. The main difference is that [16] lacks a cryptographically strong definition of the object constructed. In particular, their construction leaks a significant amount of information about their analog of R, even though it leaves the adversary with provably “many valid choices” for R. In retrospect, their notion can be viewed as an (information-theoretically) one-way function, rather than a semantically-secure key encapsulation mechanism, like the one considered in this work. Nonetheless, their informal notion is very closely related to our secure sketches, and we improve their construction in Section 5.
> Juels和Sudan[16]提供了除Hamming之外的第一个度量结构:他们为集差度量构造了一个“模糊拱顶”方案。主要的区别是[16]缺少构造的对象的加密强定义。特别是建设泄漏大量的信息的模拟R,尽管它给敌人留下证明地“许多有效的选择”R .回想起来,他们的概念可以被视为一个(information-theoretically)单向函数,而不是一个安全语义的关键封装机制,如考虑这项工作。尽管如此，它们非正式的概念与我们的安全草图非常相关，我们将在第5节改进它们的构造。

> Linnartz and Tuyls [18] define and construct a primitive very similar to a fuzzy extractor (that line of work was continued in [28].) The definition of [18] focuses on the continuous space Rn, and assumes a particular input distribution (typically a known, multivariate Gaussian). Thus, our definition of a fuzzy extractor can be viewed as a generalization of the notion of a “shielding function” from [18]. However, our constructions focus on discrete metric spaces. 
> Linnartz和Tuyls[18]定义和构造了一个非常类似于模糊提取器的原语([28]继续了这一行的工作)。[18]的定义关注于连续空间Rn，并假设一个特定的输入分布(通常是已知的多元高斯分布)。因此，我们对模糊提取器的定义可以看作是对[18]的“屏蔽函数”概念的概括。然而，我们的构造集中在离散度量空间上。

> Work on privacy amplification [3,4], as well as work on de-randomization and hardness amplification [14,24], also addressed the need to extract uniform randomness from a random variable about which some information has been leaked. A major focus of research in that literature has been the development of (ordinary, not fuzzy) extractors with short seeds (see [26] for a survey). We use extractors in this work (though for our purposes, pairwise independent hashing [3,14] is sufficient). Conversely, our work has been applied recently to privacy amplification: Ding [9] uses fuzzy extractors for noise tolerance in Maurer’s bounded storage model.
> 关于隐私放大的研究[3,4]，以及去随机化和硬度放大的研究[14,24]，也讨论了从一些信息已经泄露的随机变量中提取均匀随机性的必要性。该文献研究的一个主要焦点是(普通的，不是模糊的)短种子提取器的发展(见[26]调查)。我们在这项工作中使用提取器(尽管对于我们的目的来说，两两独立的散列[3,14]就足够了)。相反，我们的工作最近被应用于隐私放大:Ding[9]在Maurer的有界存储模型中使用模糊提取器来容忍噪声。

## Extensions

> We can relax the error correction properties of sketches and fuzzy extractors to allow list decoding: instead of outputting one correct secret, we can output a short list of secrets, one of which is correct. For many applications (e.g., password authentication), this is sufficient, while the advantage is that we can possibly tolerate many more errors in the password. Not surprisingly, by using list-decodable codes (see [13] and the references therein) in our constructions, we can achieve this relaxation and considerably improve our error tolerance. Other similar extensions would be to allow small error probability in error-correction, to ensure correction of only average-case errors, or to consider nonbinary alphabets. Again, many of our results will extend to these settings. Finally, an interesting new direction is to consider other metrics not considered in this work.

> 我们可以放松草图和模糊提取器的纠错特性来允许列表解码:我们可以输出一个简短的秘密列表，而不是输出一个正确的秘密，其中一个是正确的。对于许多应用程序(例如，密码身份验证)，这就足够了，而优点是我们可以容忍更多的密码错误。毫不奇怪，通过在我们的结构中使用列表可解码代码(参见[13]和其中的引用)，我们可以实现这种放松，并大大提高我们的容错能力。其他类似的扩展是允许在错误纠正中出现小的错误概率，以确保只纠正平均大小写错误，或者考虑非二进制字母。同样，我们的许多结果将扩展到这些设置。最后，一个有趣的新方向是考虑本工作中未考虑的其他指标。

TBD。。。
