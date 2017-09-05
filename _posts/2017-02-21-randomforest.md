---
layout: post
title:  "Boosting Model: AdaBoost GBDT和XGBoost"
date:   2017-02-21 00:00:04
categories: liuqianchao update
---
### 1. AdaBoost
&emsp; AdaBoost是借助Boosting思想解决二分类问题。其模型的基本思路是通过学习多个分类器（比如单层的决策树Decision Stump），每一轮学习新的分类器，更加关注上一轮被错误划分的样本点。因此该模型需要重点关注两个点：1.如何确定分类器的权重；2.如何调整样本权重，从而在下一轮中更加“关注”错分的样本。    
&emsp; 介绍Adaboost可以从不同的角度来入手，比如通过列出单回合的指数损失函数$$e^{-\alpha_t f(x)h_t(x)}$$，令其值最小化，可以得到新一轮的分类器如何设置权重；通过列出additive model的目标函数$$e^{-f(x)(H_t(x)+h_{t+1}(x))}$$使用泰勒展开，可以推导出样本权重的调整规则。但下文将直接使用AdaBoost算法规则的Intuition来解释分类的权重、样本的权重为什么这么设置。

&emsp; 首先给出分类错误率的定义，对于第$$t$$轮，指示函数$$I(·)$$，样本权重$$u_i$$：

$$\varepsilon_t = \frac{1}{N}\sum_{i=1}^{N}u_i I(y_i \neq \tilde{y_i})$$

&emsp;其实可以证明最小化指数损失函数等价于最小化错误率。

&emsp; 在生成一个分类器后，我们要生成该分类器的权重。理所应当的想法是该权重和分类器的错误率相关：当错误率为$$\frac{1}{2}$$时，分类器相当于乱猜，权重应该为0；如果分类器的错误率为0，则权重应该非常大。则权重可以设置为：

$$\alpha_t = \frac{1}{2}ln\frac{1-\varepsilon_t}{\varepsilon_t}$$

&emsp; 再考虑样本权重设置，我们希望本轮的分类器在处理下一轮调整后的样本时，表现地非常差：

$$\frac{u_{t+1}I(y_i \neq \tilde{y_i})}{u_{t+1}I(y_i \neq \tilde{y_i}) + u_{t+1}I(y_i = \tilde{y_i})} =\frac{1}{2}$$

&emsp; 因此在$$t+1$$轮，我们应该将上一轮错误划分样本和正确划分的样本权重调整为相同。一种最直接的调整方式就是乘上对方，并除以一个固定值(两者之和)，即可以变形为：分别乘以$$(1-\varepsilon_t)、\varepsilon_t$$，在做缩放(Scale)，可以得到:

$$incorrect  \leftarrow incorrect \times \sqrt{\frac{1-\varepsilon_t}{\varepsilon_t}} =incorrect \times e^{\alpha_t}\\
correct  \leftarrow correct \times \sqrt{\frac{\varepsilon_t}{1-\varepsilon_t}} =correct\times e^{-\alpha_t}
$$

&emsp; 这里最后再提一个概念，即样本权重调整(Weight Resampling)，在具体操作时不是将权重直接乘以$$y$$，而是$$u_i loss(y_i, \tilde{y_i})$$。对于无法进行该操作的分类器，可以采用Resampling的方法，即通过调整抽样概率实现样本权重的调整。

### 2. GBDT和XGBoost

&emsp; 随机森林，作为集成学习的代表算法，其随机体现在两方面：

- 随机采样；   
- 在每个节点选择属性时，首先随机选择k个子属性，然后从这个子集中选择最优属性用于划分。     

#### 2.1 回归树
在讲GDBT(Gradient Boosting Decision Tree)之前, 我们先讲**回归树**。

&emsp; 不同于分类树算法使用熵作为分枝的条件，回归树采用最小二乘误差来确定分支的条件，具体操作是：

1. 挑选待分割的变量$$x$$(依次遍历各待分割变量，找到最好的那个),   
2. 固定变量x后，遍历地设置切分点s，划分为$$x<=s$$和$$x>s$$两个子节点，子节点的输出值是各自的均值，然后计算两个子节点各自的二乘误差并求和。挑选使二乘误差最小的切分点。对子节点递归上述操作，指导满足最终停止条件。

#### 2.2 提升树
**Boosting** 意为提升；其在操作时，先从初始训练集训练出一个基学习器；再根据学习器的结果对训练样本的分布进行调整，使之前预测错误的样本在后续的预测中得到更多的关注，通常预测准确性高的学习器具有较高的学习的权重。

&emsp; Boosting 使用公式来表达：

$$
f(x) = w_0 + \sum_{m=1}^{M}w_m\phi_{m}(x)
$$

**提升树（Boostring Tree）**则是在每轮生成树的过程中使用前面上一颗树各样本的残差项作为预测值($$y_i$$)，去生成新的树。

#### 2.3 梯度提升树

上面我们看到在提升树中，我们使用残差作为下一轮学习的目标值，但当目标函数不在是最小二乘($$\frac{1}{2}(y-\hat{y})^2$$的梯度为$$y-\hat{y}$$，即残差)
&emsp; GDBT的一般步骤:

1. 初始化一个常量（可以理解为只有根结点，没有进行分枝的树）:

$$
f_{0} = arg min_c\sum_{i=1}^N L(y_i, c)
$$

2. 对于第1到M中的第m个树，依次在该轮开始时计算梯度$$\frac{\delta L(y_i, f_{m-1}(x_i))}{\delta f_{m-1}(x_i)}$$作为下一轮学习的目标值。
3. 得到该轮的学习目标值$$r_{mi}$$后，拟合一个回归树。得到第$$m$$课树的叶结点区域$$R_{mj}$$，其中$$j\in 1,2,3..J$$，且$$J$$为叶子结点的数目。这时给出$$f_m(x)$$在boosting时的更新方程：

$$
c_{mj} = argmin_{c} \sum_{x_i \in R_{mj}} L(y_i, f_{m-1}(x_i)+c)\\
f_m(x) = f_{m-1}(x) + \sum_{j=1}^Jc_{mj}I(x\in R_{mj})
$$
