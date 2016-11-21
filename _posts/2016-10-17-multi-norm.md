---
layout: post
title:  "多元正态分布"
date:   2016-10-16 10:00:04
categories: liuqianchao update
---


### 1. What is multivariate normal distribution?   

&emsp; 首先我们知道，标准正态分布的概率密度函数为：

$$f(x) = \frac{1}{\sqrt{2\pi}}e^{-\frac{x^2}{2}}$$  

&emsp; 对于一个一般的正态分布X，由$$\frac{X-u}{\sigma} \sim N(0,1)$$，我们可以由“随机变量的变换”得到X的密度函数：

$$f(x) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-u)^2}{2\sigma^{2}}}$$

&emsp; 到此，我们讨论的范畴还局限于一元正态分布，我们假设存在随机向量$$Z=(X_1,X_2...X_p)^T$$，独立同分布于N(0,1);由相互独立的假设，我们可以知道$$Z \in R^P$$，其概率分布为

$$f(x_1,x_2..x_p) = \prod_{i=1}^p \frac{1}{\sqrt{2\pi}}e^{-\frac{x_{i}^2}{2}}$$

&emsp; Z的均值为$$(0,...,0)^T$$，协方差矩阵为$$I$$（非对角线元素因为相互独立，协方差为0），即为$$Z \sim N_p(0,I)$$。

&emsp; 同样，我们进行变换$$X = u + AZ$$，其中$$u$$为p维常数向量，$$A$$为p阶非退化矩阵。由此，得到一般多元正态分布的密度函数、均值、方差；其中均值为u，方差为$$AA^T$$，记作$$\sum$$。

&emsp; 需要注意，以上的结论的假设，多元变量之间相互独立；对于正态分布，如果相关系数（协方差）为0，则相互独立。

&emsp; **边际分布**，是指对于多维随机变量，只包含其中部分变量的概率分布。比如对于随机变量$P(X_1, X_2)$：

$$P(X_1) = P(X_1,\mid X_2) = \sum_i P(X_1 \mid X_{2i})$$

### 2. 多元正态总体的参数估计

&emsp; 假设一个多元正态分布的样本$$X_{n,P}$$，即每个样本P个维度，共n个样本。则样本离差矩阵是这样定义的:$$A=\sum_{\alpha=1}^{n}(X_{(\alpha)}-( \bar{X}))(X_{(\alpha)}-( \bar{X}))^T$$，A服从于参数为$$(n-1, \sum)$$的Wishart分布。


### 3. Linear Regression

#### 3.1 Simple regression 

&emsp; Simple regreesion是用来对两个变量进行回归的方法，解释变量$$x$$是非随机变量，被解释变量$$y$$是随机变量，可以通过相关系数(Correlation)来表示，两个变量之间的线性关系强弱。

$$r = \frac{\sum(x-\bar{x})(y-\bar{y})}{\sqrt{\sum(x-\bar{x})^2 \sum(y-\bar{y})^2}}$$

&emsp; $$r$$只能用以形容线性关系的强弱，$$r=0$$不表示两个变量之间没有相关关系，比如$$y = x^2$$产生的一组系列的r为0，但其存在相关关系。

&emsp; 一元回归的结果是：

$$\hat{y} = b_0 + b_1 x$$

&emsp; 在此基础上，我们定义残差(Residual)：

$$e = \hat{y} - y$$

&emsp; 一般我们使用残差最小二乘的结果，来确定一元回归的参数$$b_0$$和$$b_1$$。这里最小二乘$$SSE = \sum (y_i-\hat{y_i})^2$$，SSE为Sum of Squared Error；同时$$RSS = \sum e_i^2$$，Residual sum of squared是同一个概念。

&emsp; 一般我们用$$R^2$$和$$RMSE$$来表示拟合效果的好坏，其中RMSE依赖于Y的scale，而$$R^2$$是无量纲的。下式中$$TSS$$(Total sum of Squares)是样本原始值和样本原始值均值的差平方：

$$R^2 = \frac{TSS-SSE}{TSS}$$

$$TSS=\sum (y-\bar y)^2$$

$$RMSE = \sqrt{MSE} = \sqrt{\frac{SSE}{n-2}}$$

&emsp; 事实上,$$R^2 = r^2$$

#### 3.2 multi linear regression 

&emsp; 对于多元线形回归的一般形式我们有：

$$y_i = \beta_0 + \beta_1 x_1 + \beta_2 x_2 ... \beta_n x_n + \varepsilon$$

$$Y = BX + \varepsilon$$

求解参数的方法有多种：比如极大似然估计、贝叶斯估计(最大后验概率)。

对于Exponential family分布，最小二乘法和极大似然法是一样的。$$\|Y - \hat{Y} \|^2$$，其中$$ \hat{Y} = BX$$,通过极大似然估计求解: $$B = (X^T X)^{-1}X^T Y $$


#### Reference
1. Websites [zhihu](https://www.zhihu.com/question/20447622)

