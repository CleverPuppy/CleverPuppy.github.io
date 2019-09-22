---
layout: post
title:  "A Survey on Cooperative Co-Evolutionary Algorithms阅读笔记"
date:   2019-09-22 07:34:42 -0500
categories: 演化算法 协同演化
---

# A Survey on Cooperative Co-Evolutionary Algorithms阅读笔记

## 说明

X. Ma et al., "A Survey on Cooperative Co-Evolutionary Algorithms," in IEEE Transactions on Evolutionary Computation, vol. 23, no. 3, pp. 421-441, June 2019.
doi: 10.1109/TEVC.2018.2868770
Abstract: The first cooperative co-evolutionary algorithm (CCEA) was proposed by Potter and De Jong in 1994 and since then many CCEAs have been proposed and successfully applied to solving various complex optimization problems. In applying CCEAs, the complex optimization problem is decomposed into multiple subproblems, and each subproblem is solved with a separate subpopulation, evolved by an individual evolutionary algorithm (EA). Through cooperative co-evolution of multiple EA subpopulations, a complete problem solution is acquired by assembling the representative members from each subpopulation. The underlying divide-and-conquer and collaboration mechanisms enable CCEAs to tackle complex optimization problems efficiently, and hence CCEAs have been attracting wide attention in the EA community. This paper presents a comprehensive survey of these CCEAs, covering problem decomposition, collaborator selection, individual fitness evaluation, subproblem resource allocation, implementations, benchmark test problems, control parameters, theoretical analyses, and applications. The unsolved challenges and potential directions for their solutions are discussed.
keywords: {evolutionary computation;optimisation;resource allocation;co-evolutionary algorithm;CCEA;complex optimization problem;multiple subproblems;separate subpopulation;individual evolutionary algorithm;multiple EA subpopulations;problem decomposition;benchmark test problems;divide-and-conquer mechanism;collaboration mechanism;EA community;covering problem decomposition;collaborator selection;individual fitness evaluation;subproblem resource allocation;control parameters;Optimization;Genetic algorithms;Resource management;Benchmark testing;Computer science;Google;Perturbation methods;Cooperative co-evolutionary algorithm (CCEA);evolutionary algorithm (EA);genetic algorithm (GA)},
URL: <http://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8454482&isnumber=8725287>

## 合作型协同演化计算的主要思想

cooperative co-evolutionary algorithms(CCEA) 合作型协同演化算法（CCEAs）使用分而治之的思想，将复杂的优化问题分解为多个子问题，这些子问题“合作”进行演化计算，然后通过合作机制来更有效地解决优化问题。

### 生物启发

1. resource-resource interaction (牛和the Rumen bacteria)
2. resource-service interaction (花粉和昆虫授粉（花提供食物）)
3. service-service interaction (anemonefish and sea anemone)

### 相比传统演化计算的优势

1. 将问题进行分解，就能够使用并行计算加速优化过程
2. 每一个子问题由一个子种群去迭代求解，有利于维持解的多样性
3. 将一个系统分解为多个子模型能够增强模型的健壮性，当模型有误差或是有错误（fails）
4. 分解的好的协同演化算法，在一定程度上可以缓和维数灾难

协同演化算法能够和不同的启发式方法相结合，为经典优化算法注入活力

### Topics

1. Problem decomposition
2. Collaborator selection
3. individual fitness evaluation
4. subproblem resource allocation
5. implementations
6. benchmark test problems
7. control parameters
8. theoretical analyses
9. applications

## 背景知识

### 问题分类

1. 完全可分问题
2. 完全不可分问题
3. K-不可分问题

## 问题分解和变量相关性

理想情况下，问题分解应该根据变量的相关性来进行。如果相关变量没有被归为同一个子成分中，CCEAs倾向陷入伪最优（pseudo-minimum）,即不是原问题的局部最优，而是由错误分解而引入的局部最优。

### 变量分组策略

- static variable grouping
- random variable grouping
- linkage learning-based variable grouping
- overlap and hierarchical variable grouping
- domain knowledge-based variable grouping
- their hybirds

### Static Variable Grouping（静态变量分组）

不使用系统化或智能化方法来发现变量之间的相互独立关系，采用简单的策略将高维决策向量转变为一系列低维的子结构，在优化的过程中，变量分组不再进行改变。

有按顺序分组和随机分组两种，能成功用在完全可分问题上，

在非完全可分的问题上，将分组的粒度变大，如1-D decomposition 变为m s-dimentional subcomponents , n = m*s， 表现会比 1-D 要好

| Algorithms | Static problem decomposition | Collaborator selection |
|---|---|---|
|CCGA|1-D decomposition | Single best collaborator, Best+ random collaborator|
|AEGL|A pool of static decompositions | Random collaborator|
|CC-GLS|Sequential sliding window(滑动窗口，问题规模超过1000)|Single best collabrator|
|CCPSO-S,CCPSO-$S_K$| m s-D subcomponents|Single best collabrator|
|GCD|Domain knowledge-based|Single best collabrator|

### Random Variable Grouping(随机分组)

静态变量分组策略，在变量分组确定后，如果两个未被分为一组的变量，即便他们是高度相关的，也不能够再归为一组了（静态变量分组确定分组后就不再改变）。随机分组随机从子结构中选择变量来打破这个固定分组后就不再改变的缺点。同时，在RG中，子结构的数量是可以固定也可以是动态的。

1. RG with fixed subcomponents size

    固定子结构大小$m$，在每一轮co-evolutional cycle中，随机从**每个**子结构中选择$s$个变量,$n=s*m$，目的是为了增加讲相关变量放入同一个分组的可能性。

    |Pros|Cons|
    |---|---|
    |for at least two cycles,将相关变量分为一组的概率相对来说较高|当*相关变量数量很多的时候*，讲相关变量分为一组的概率还是很低，for at least one cycles|

2. RG with dynamic subcomponents size

    在静态分组和RG with fixed subcomponents size中，都需要确定子结构的尺寸$s$

    |Small $s$| large $s$|
    |---|---|
    |适合可分问题|越大则将相关变量分为一个组的概率越高|

    相关策略：
    - 在一定范围内随机变化$s$
    - 通过计算某些条件来自适应
    - 逐渐减小
    - 启发式调整based on domain knowledge

![talbe 2](table2.jpg)

### Variable grouping based on interaction learning

static grouping 和 random grouping都有着如下两个缺点：

1. 需要预先确定子结构的size(s) ${ s_1, ... , s_t}$
2. 选定好子结构数量后，决策变量被均匀分到不同子结构中，这是对问题的不合理假设。

Variable interactions describe the structure of a problem.基于变量相互关系来学习问题结构，然后进行分解。
基于这种思想提出了很多检测变量相互关系的方法，具体有:

- perturbation
  
  设计算子，检测分别改变变量$i,j$带来的适应度变化，以及同时改变$i,j$带来的适应度变化，来确定变量间的关系，在进行分组。
  如：
  $$\Delta_{i,j} = |\Delta_if(x) + \Delta_jf(x) - \Delta_{i,j}f(x)|$$

- statistical model

  在分组前进行统计分析，根据以下方法对变量或目标方程进行测量：

  1. 变量独立性测量，如Pearson correlation coefficient(PCC),entropic epistasis, mutual information
  2. Correction between variable and objective functions, including cumulative PPC between them and the maximal information coefficent
between $x_j$ and $\partial f(X)/ \partial x_i$
  3. 变量值的分布，比如变量均值、方差、二元联合分布和单变量分布之间的KL距离

  *在部分情况下有优势*

- distribution model

  pairwise interactions- the bivariate marginal distribution algorithms
  higher order interactions - FDA and Bayesian optimization algorithms(BOA)

- approximate model
  
  使用代理模型估计高昂代价问题，如The high dimensional model representation (HDMR)，有点类似拉傅里叶级数

  ![HDMR](wpspdf_rjY3Zm6UYM.jpg)

- linkage adaption

  使用特殊设计的演化机制以及表示方法，使用特殊标志位将变量（或基因）分组

### Variable grouping based on domain knowledge*

使用先验知识来简化问题，比如利用最短距离信息等

### Overlap and hierarchy variable grouping*

重叠指的是在问题分解中，同一个变量被分为多个子问题中；

- ramdom overlap grouping(OG)
- neighbor OG
- centered OG
- parents OG
- loci OG
- Markov grouping
- Clique grouping

层级结构将问题分为多个level

![overlap and hierarchy](wpspdf_UPofy3FVDH.jpg)

### Hybird variable grouping

混合以上多种方法，进行Variable grouping.

## Collaborator selection strategies of CCEA

现存的选择策略思路主要从两个因素来考虑：

1. Collaborator selection pressure. 使用贪心策略从子结构中选择个体来组成解
2. Collaborator pool size. 用来计算适应度以及演化子种群的解的个数

- 个体为中心
- 群体为中心 

- 单目标优化问题（single objective optimization problems,SOPs）
- 多目标优化问题(Multiobjective optimizatiton problems, MOPs)

### Collaborator selection strategies for SOPs

1. Collaborator selection **for single complete solution**

  |选择策略|说明|
  |---|---|
  |single best|在完全可分问题表现好，不可分的情况下容易陷入局部最优或是纳什均衡|
  |single worst|一般不是最优解|
  |random|在快速变化的环境中，效果比single best好，但是收敛速度慢|
  |elite|当K=1是，就是single best；可以设计为动态调整K值，使其逐步变为1|
  |roulette/tournament-based|在low-epistasis的优化问题中，表现比single best和random好|
  |Linked|在high epistatis问题中，表现很好|
  |neiborhood-based| |
  |||

2. Collaborator selection for **multiple complete solution**

  |选择策略|说明|
  |---|---|
  |complete collaborator selection strategy|每个子种群的每个个体都与其它个体结合计算适应度，运算量巨大，当种群足够大的时候，能够保证收敛到全局最优|
  |archive-based|在每一代，子种群中的每个个体都和其余nondominated个体组合计算适应度；问题是在迭代的末期，nondominated个体数会越来越大，导致计算量越来越大；可以采用固定nondominated集合个数的策略|
  |hybird|组合不同的选择策略，如best+random,best+elite等|
  |自适应选择策略|通过组合的个体数不断减小来进行自适应（考虑时间变化、或种群多样性、以及操作成功率）|
  |||

### collaborator selection strategies for MOPs

一些适用SOPs的选择策略同样适用与MOPs，如single-best,best+random,elite,roulette/tournament-based,linked

额外三种

1. stochastic approach

  随机选择多个nondominated解，作为子种群的代表

2. preference-based approach

  每个子种群选择一个Nondominated个体

3. competition-based approach

  不同的子种群进行竞争，优秀种群/个体采样概率更高

## Individual fitness assignment and subproblem resource allocation

### Individual fitness assignment

1. 基于一个或者多个完整解,也可组合多个完整解，比如采取最优，平均或最差，轮盘赌，biasing technique     - 对于子种群的个体来说有巨大的偏差（a strong bias）
2. 其它方法：利用Pareto 最优解；使用父代个体的fitness估计一些子代个体的fitness；niching methods
3. 启发式方法：训练神经网络进行评估

### Computational resource allocation for subproblems

1. 均等分
2. 基于随机策略
3. 根据长期“贡献值”
4. 根据最佳“贡献值”
5. 根据不同的子种群大小

## implementation of CCEAs

### 基于不同的元启发式算法

1. GA
2. GP
3. 演化策略
4. 蜂群
5. 蚁群
6. competitive co-evolution
7. 人工免疫系统
8. 代理协助搜索
9. memetic

其中，DE和PSO对于连续优化问题，是最广泛使用的；基于蚁群算法的CCEAs对于组合优化问题表现很好；基于代理模型的对昂贵代价问题表现很好；GP-based用来决策问题的进行问题分解；Memetic和hybird方法在解决大规模问题更有优势。

也可组合传统EAs和CCEAs，因为CCEAs容易陷入局部最优。

### 并行化

1. CPU
2. GPU
