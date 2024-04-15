---
layout: post
title: Apriori algorithm
date: 2024-04-15 17:35:00 +0800
categories: [ML, Data Mining]
tags: [Data Mining]     # TAG names should always be lowercase
math: true
mermaid: true
---

# Apriori算法

### 一、Apriori算法的简介

​		Apriori算法是第一个关联规则挖掘算法，也是最经典的算法。它利用逐层搜索的迭代方法找出数据库中项集的关系，以形成规则，其过程由连接（类矩阵运算）与剪枝（去掉那些没必要的中间结果）组成。该算法中项集的概念即为项的集合。包含K个项的集合为k项集。项集出现的频率是包含项集的事务数，称为项集的频率。如果某项集满足最小支持度，则称它为[频繁项集](https://baike.baidu.com/item/频繁项集/1573014)。

​		这一部分纯属在网上摘抄的，其实我也不知道啥意思。

### 二、Apriori算法论文：

​		这里我就要推荐小伙伴们可以去看看Apriori算法的论文了

​		*Fast Algorithms for Mining Association Rules*

​		因为文章是全英文，我自己能力有限，所以只截取了相关算法的伪代码。

### 三、论文讲解

​		在了解Apriori算法之前，我们应该了解一下什么叫做关联规则？

![在这里插入图片描述](https://img-blog.csdnimg.cn/716354ca51d54281955b9ae60a457ce4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56iL5bqP54y_SG9hcnk=,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)

​		看见这满是英文的论文，是不是没有看下去的欲望，但是只要耐心地看，其实都是数学符号。

​		这一段的意思是，先定义一个项目集 ***L*** 中，( ***L*** 是包含全集{ i1i2i3i4i5.....im}的所有子集（空集除外）的集合)，然后交易数据库 ***D*** 中的每一条数据都能在 项目集 ***L*** 中找到，(所以我才说 ***L*** 是包含集合{ i1i2i3i4i5.....im}的所有子集的集合)，交易数据库中的每一条数据都会有个唯一的编号，（这个没啥，可能是为了方便统一查询），这一段的后面则是说 如果 频繁项集 ***X*** 和频繁项集 ***Y*** 属于交易数据 ***D*** 中的某条交易数据  ***T*** ,两个集合没有重合的元素（项集 ***l***），且隐含着只要有 ***X*** 就有一定概率有 ***Y*** 这一关系，则说明 ***X*** 与 ***Y*** 之间存在某种规则，而这种规则的强弱，由这种概率的大小决定的。这概率我们通常用 **置信度**来表示，置信度大于最小置信度（人为设定的）则说明两个集合（在交易数据库中则称商品）有强关联即 ***X =>Y***

​		置信度 ***confidence*** ：

​			当频繁项集 ***X =>*** 频繁项集 ***Y*** 时

​				***confidence*** = support(***X*** U **Y**) / support( ***X*** )  >minconfidence

​		支持度 ***support*** :

​			就是项集 ***l*** 在交易数据中出现的频率。

​		例如：交易数据库D = {ABCD, BCE, ABCE, BDE, ABCD}

​			项集 ***l*** = {BC} 在D中出现在了{ABCD}，{BCE}，{ABCE}，{ABCD} 这四条交易数据中，所以其频率 ***frequency*** = 4/5 = 80% 

​		频繁项集 ***frequent items***:

​			即：支持度大与最小支持度的项集（人为设定）

### 四、Apriori算法解释

​		强关联规则如何去求，想必看到这里的小伙伴都已经清楚了,但是我们如何去找上述所说的频繁项集呢？

​		论文中给出我们求解频繁项集的算法，我们就来分析分析吧！

#### 最大频繁项集：

​		论文定义了一个叫做最大频繁项集的东西，顾名思义，就是频繁项集的老大呗！但是最大频繁项集不一定只有一个，为什么？因为一个家庭一个老大，但是一组家庭怎么会只有一个老大呢？难道不是一群老大么？好吧！扯远了，但是确实比较形象。官方的定义是：

***最大频繁项集是各频繁k项集中符合无[超集](https://baike.baidu.com/item/超集/1059571)条件的频繁项集。***

例如：D = {ABCD, BCE, ABCE, BDE, ABCD} 中 {ABCD，BCE}是最大频繁项集，因为在项集中没有比它更大了，那有人要问了，BCE为什么是最大频繁项集，你在项集中有比它更大，且包含E的项集么？没有，所以它就是老大。

#### 候选项集：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d4031dbf2b9546ec8c3d9f11d9f466f8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56iL5bqP54y_SG9hcnk=,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)


​		论文定义了一个叫做候选集的东西，***Candidate itemsets*** ：

​			什么叫候选集？其实很容易理解，就是发现更大的频繁项集的基础项集。举个例子：

​			存在项目集 S = {AB,AC,BC,BD} 那么我通过这个基础的2-项集 （每个元素的项数为2）我很容易找到我的 3-项集 {ABC,ABD,BCD}，此时的 3-项集，我们称为候选项集，当然其中的 3-项集不一定全是频繁的，所以我们需要剪枝。

#### 算法思想：

​		好！有了之前的知识铺垫我们正式进入这篇文章的核心部分，Apriori算法思想。
​![在这里插入图片描述]()
../assets/img/favicons/android-chrome-512x512.png


​		又给你们整张图片，好家伙，你们肯定在怀疑这家伙啥都没说，是来教英语吧！其实我挺想教英语的，咳咳，不扯远了。

​		言归正传，说实话，这张表，我也没看懂啥，但是我仔细研究了一下，还是有道词典比较厉害，翻译了啥？

​		Lk是频繁K-项集，Ck是由Lk产生的候选项集，最后一个（这个一杠我打不出来）则是候选项集中与交易数据库中有联系的。

​		好，介绍完这三个变量后，我再整张图片。
​![在这里插入图片描述](https://img-blog.csdnimg.cn/da9a04faca014311a7e6a7308b2ba6ad.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56iL5bqP54y_SG9hcnk=,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)


​		兄弟们这就是硬核了，我比较菜所以弄了几天才看懂并把算法实现，想必各位兄弟都比我厉害，能看懂所以就跳过！这部分不讲。

​		哈哈，说笑的，我这人又菜又爱显摆，我就要讲，你们都给我来康康。

​		其实原理很简答，我挑几个我认为比较难懂的地方（别喷，是我菜了）。

​		算法思路：

​			3）就是先通过K-1-项集产生K项集，（假设到了K项集还没找完最大的频繁项集）

​			5）然后将候选项集依次与交易数据库中交易数据作对比，获取Ck中在交易数据库中出现的元素组成的集合，就这一步我当时卡了挺久的，所以我就简单举个例子：

​			我的交易数据是这个样子 D = {ABCD, BCE, ABCE, BCD, ABCD}，我的Ck是这个样子Ck={AB, AC, AD, AE, BC, BD, BE, CD, CE, DE},经过5）之后，我得到了Ct = {AB, AC, AD, AE, BC, BD, BE, CD, CE,},这就去除了DE，简化了下一步操作。

​			其他的论文上都已经写的很清楚了。下面再解释其中的用到的函数：

​		***apriori-gen：***

​	![在这里插入图片描述](https://img-blog.csdnimg.cn/cf455f3c18494277a32eaaf52fd57baa.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/187d5189cb654936991f29bb6012613b.png#pic_center)


​		这个函数的思想是利用的如果一个项集的的子集不是频繁的那么它也一定非频繁的，所以这个函数采取了逐一对比，知道确定最后一个项不同，然后将其中的一个元素的最后一项加入到另一个元素的末尾。我们依旧使用例子来分析：

![在这里插入图片描述](https://img-blog.csdnimg.cn/7033db3b57c74539b1f7e2becc2369fd.png#pic_center)


​		论文给了一个例子，其中 {1234}是由{123}和{124}对比拼接产生的。并且发现{1234}的3-项子集{234}也在，所以就能确定。其频繁4-项集一定在，{1345}为什么不在，我就不过多赘述了。

​		Apriori算法核心思想就是这一些了，论文剩下的部分我也没怎么去解读，所以就不在这里指鹿为马，胡编乱造了。以下就是我自己实现的Apriori算法，可能写得不是很好，但是勉强能够运行能找出频繁项集。

### 五、Python实现

例：交易数据D = {ABCD, BCE, ABCE, BDE, ABCD}找出其中频繁项集：

```python
import copy

minsupport = 2  # 最小支持度的频数


# 除去子集不是频繁项集的候选项集
def subset(sen_c_items, item):
    sub_set = []
    for i in sen_c_items:
        candidate = set(i)  # 将K-1项集中的候选项变成集合形式
        t_record = set(item)  # 将交易数据库中的数据项变成集合形式
        if candidate.issubset(t_record):
            sub_set.append(i)  # 保留子集部分
    return sub_set


# 找出K项候选集
def apriori_gen(sub_k_items, n):
    sen_k_items = set([])
    for p in range(len(sub_k_items)):
        for q in range(p + 1, len(sub_k_items)):
            flag = True
            p_item = list(sub_k_items[p])
            q_item = list(sub_k_items[q])
            for i in range(n):
                if p_item[n - 1] == q_item[n - 1]:
                    flag = False
                    break
                elif p_item[i] != q_item[i] and i < n-1:
                    flag = False
            if flag:
                c = list(sub_k_items[p] + q_item[n - 1])  # 将q中的最后一项添加到p中
                c.sort()
                if has_frequent_subset(c, sub_k_items, n):
                    c = "".join(c)
                    sen_k_items.add(c)
    print("候选", n + 1, "-项集：", sorted(sen_k_items))
    return sen_k_items


def has_frequent_subset(c, freq_items, n):
    num = len(c) * n
    count = 0
    flag = False
    for c_item in freq_items:
        if set(c_item).issubset(set(c)):
            count += n
    if count == num:
        flag = True
    return flag


# 剔除不频繁的项集
def op_freq_item(d):
    for item in list(d.keys()):
        if d[item] < minsupport:
            del d[item]
    l = sorted((d.keys()))
    return l


if __name__ == '__main__':
    dataset = ["ABCD", "BCE", "ABCE", "BDE", "ABCD", ]
    d = {}
    for t in dataset:  # 挑选频繁1项集
        for index in range(len(t)):
            if t[index] in d:
                d[t[index]] += 1
            else:
                d[t[index]] = 1
    l1 = op_freq_item(d)  # 剔除不符合的1项集
    sub_c_items = l1
    c_items_freq = {}  # 用于存放K-1候选项集，并统计其出现的频数
    freq_items = [sub_c_items]  # 保存频繁项集
    n = 0
    while True:
        n += 1
        print("寻找", n + 1, "-候选项集")
        sen_c_items = list(apriori_gen(sub_c_items, n))  # 找到K项集 sen_c_items
        for t in dataset:
            k_subset = sorted(subset(sen_c_items, t))  # 获取K项集包含候选项集的item的子集 new_c_item
            print(t, "的子集：", k_subset)
            for c_item in k_subset:
                if c_item in sen_c_items and c_item in c_items_freq:
                    c_items_freq[c_item] += 1  # 找到了加1
                else:
                    c_items_freq[c_item] = 1  # 未找到则创建并初始数量为1
        print("待挑选的频繁：", c_items_freq)
        if len(op_freq_item(c_items_freq)):
            sub_c_items = op_freq_item(c_items_freq)  # 选出K-1候选项集
            freq_items.append(sub_c_items)  # 将频繁K项集加入频繁项集freq_items中
            print(sub_c_items)
        else:
            break
        c_items_freq.clear()  # 清楚字典里的元素
        print()
    print("频繁项集：", freq_items)


```

这里我只是简单地实现Apriori算法的一个例子，实际上Apriori算法用的并不是很多，从代码上就不难看出，Apriori算法的空间复杂度合时间复杂度都很高，所以为了解决这种问题，大牛们又提出了Close算法，FP-Tree算法，这些算法大大降低了遍历交易数据的次数，提高了算法性能。但是不管怎么说，Apriori算法的提出在关联规则这一方面的确实是划时代的存在。