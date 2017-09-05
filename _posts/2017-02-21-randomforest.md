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

上面介绍了Adaboost，而Adaboost实际上可以用最速下降法(或者我们下面要讲的Gradient Boosting)来解释，其实质是一个使用指数损失函数解决二分类问题的特殊情况下的最速下降模型。下文要考虑的是，如果我们的损失函数不再是使用指数损失函数，更一般情况下的“Adaboost”便是梯度提升模型。


#### 2.1 回归树
在讲GDBT(Gradient Boosting Decision Tree)之前, 我们先讲**回归树**。

&emsp; 不同于分类树算法使用熵作为分枝的条件，回归树采用最小二乘误差来确定分支的条件，具体操作是：

1. 挑选待分割的变量$$x$$(依次遍历各待分割变量，找到最好的那个),   
2. 固定变量x后，遍历地设置切分点s，划分为$$x<=s$$和$$x>s$$两个子节点，子节点的输出值是各自的均值，然后计算两个子节点各自的二乘误差并求和。挑选使二乘误差最小的切分点。对子节点递归上述操作，指导满足最终停止条件。

#### 2.2 提升树
&emsp; **Boosting** 意为提升；其在操作时，先从初始训练集训练出一个基学习器；再根据学习器的结果对训练样本的分布进行调整，使之前预测错误的样本在后续的预测中得到更多的关注，通常预测准确性高的学习器具有较高的学习的权重。

&emsp; Boosting 使用公式来表达：

$$
f(x) = w_0 + \sum_{m=1}^{M}w_m\phi_{m}(x)
$$

&emsp; **提升树（Boosting Tree）**则是在每轮生成树的过程中使用前面上一颗树各样本的残差项作为预测值($$y_i$$)，去生成新的树。

#### 2.3 梯度提升树
&emsp; 有些时候我们通过谨慎地区分各个概念可以方便我们理解各个模型的关联关系，这里我们要需要谨慎对待的概念是GB算法(Gradient Boosting Algorithm)和GBDT(Gradient Boosting Decision Tree， 又名Multivariate Adaptive Regreesion Tree或MART)，后者只是前者的一种具体实现。下面首先要介绍Gradient Boosting算法:一种通过确定任意损失函数的梯度，以及更新步长来“boosting”的算法；接下来以GBDT为例，介绍Gradient Boosting算法的一种通过回归树作为弱学习器的实现。

##### 2.3.1 Gradient Boosting
&emsp; 梯度提升算法是使用函数梯度下降(Function Gradient Descent)的方法，使用连续多个函数（分类器）沿着函数梯度下降的方向做优化，从而使损失函数最小化。理论上任意可以微的损失函数都适用于该算法。下面是算法实现的基本步骤：

- 初始化常数项$$\gamma$$作为初始函数$$F_0$$，该参数里可以理解为线性模型的常数项bias

$$F_0(x) = argmin_{\gamma} \sum_{i=0}^N L(y_i, \gamma)$$

- 对于迭代轮次m=1到M:
  - 计算梯度残差(gradient residual)：$$r_{im} = - [\frac{\delta L(y_i, F(x_i))}{\delta F(x_i)}]_{F(x) = F_{m-1}(x)}$$
  - 适用学习器$$h_m(x)$$拟合该残差, 训练集为$$\{(x_i, r_{im})\}$$
  - 计算步长$$\gamma_m= arg min_{\gamma}\sum_{i=1}^{N}L(y_i, F_{m-1}(x_i)+\gamma h_m(x_i))$$
  - 适用更新方程：

$$F_m(x) = F_{m-1}(x)+\gamma_m h_m(x)$$

- 得到最终模型：

$$F_M(x)$$

##### 2.3.2 Gradient Boosting Decision Tree

&emsp; 而GBDT是将上述GB算法中的学习器替换为回归树，并对固定步长$$\gamma_m$$做了修正，令每个叶子结点的$$\gamma$$相互独立，因此有$$\gamma_{jm}$$，其中$$j\in \{1..J_m\}$$为叶子结点的序号。   
&emsp; 因此GBDT与一般形式的GB算法相比，只在一维最优化步长$$\gamma_m$$处有差异，下面给出步长的方程式：

$$
\gamma_{jm} = argmin_{\gamma} \sum_{x_i\in R_{jm}} L(y_i, F_{m-1}(x_i)+\gamma)\\
F_m(x) = F_{m-1}(x) + \sum_{j=1}^{J_m}\gamma_{jm} I(x\in R_{jm})
$$

### 3 Boosting算法为什么有效


### 4 Staking模型融合
&emsp; Boosting的思想是通过若干个较弱的学习器通过组合的方法得到模型的最终的预测结果，而Stacking的思想是通过若干学习器（第一层：初级学习器M个）分别学习原始输入，比如一个样本$$(X, y)$$依次输入三个初级学习器，分别得到$$\hat{y_1}, \hat{y_2}, \hat{y_3}$$，将其作为该样本下一层学习器（次级学习器）的输入，这样我们就把该样本数据重构为$$(\hat{Y}, y)$$，其中$$\hat{Y} = \hat{y_1}, \hat{y_2}, \hat{y_3}$$作为次级模型的输入。   
&emsp; 但是上述的做法有较大的可能会过拟合（随机性太少），则可以采用K折交叉检验（或者留一法）。比如k折交叉检验中，对于第一组样本数据，我们使用第2到K组数据分别作为训练集，训练初级学习器，然后依次对第一组样本的各个样本进行预测，得出预测值$$\hat{y_{i1}}, \hat{y_{i2}}, \hat{y_{i3}}..\hat{y_{iM}}$$作为第一组样本中样本$$i$$在次级学习机的输入。
