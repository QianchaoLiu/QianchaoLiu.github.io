---
layout: post
title:  "多元正态分布"
date:   2016-10-16 10:00:04
categories: liuqianchao update
---
### 0. 一元统计量
&emsp; 常用的一元假设检验：

1. 方差已知，判断一组变量均值是否相等假设：U检验，$$U = \frac{\bar{x}-\bar{u}}{\sigma/\sqrt{n}}$$

2. 方差未知，判断一组变量是否等于假设：t检验：$$t = \frac{\bar{x}-\bar{u}}{S_n/\sqrt{n}}$$(wiki关于方差分析和t检验区别的描述：ANOVA is conceptually similar to multiple two-sample t-tests, but is more conservative (results in less type I error) and is therefore suited to a wide range of practical problems.)

3. 方差相等且未知的两个样本之间：t检验：$$t = \frac{\bar{x_1}-\bar{x_2}}{S_w/\sqrt{\frac{1}{n_1} +\frac{1}{n_2} }}$$，其中$$S_w = \frac{(n_1-1)S_{n1}^2 + (n_2-1)S_{n2}^2}{n_1 + n_2 -2}$$

4. 均值已知，判断方差是否与假设相等：卡方检验，$$\chi^2 = \frac{\sum_{i=1}^{n}(x_i - u)^2}{\sigma_0^2}~\chi^2(n)$$

5. 均值未知，判断方差是否与假设相等：卡方检验，$$\chi^2 = \frac{\sum_{i=1}^{n}(x_i - \bar{x_i})^2}{\sigma_0^2}~\chi^2(n-1)$$

5. $$u_1$$与$$u_2$$未知的两个正态母体，检验其方差是否相等：F检验,$$F= \frac{s_{n1}^2}{s_{n2}^2}$$,其中$$s_{n1} = \frac{\sum_i^{n1}((x_i)-\bar{x})^2}{n_1-1}$$

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



### 2. 多元正态分布的性质、参数估计

#### 2.1 多元正态分布的性质

&emsp; **线性组合性质**。某组向量服从多元正态分布，把它看作一个个体，则其与常数(向量或矩阵)的线性组合扔服从多元正态分布(维度未必为原始的p)；其与同维度的多元正态分布的个体的线性组合仍服从多元正态分布，且维度仍为p。

&emsp; 对于单个多元正态分布，$$X \sim N_P(\mu, \Sigma)$$，则向量$$a$$或矩阵$$A$$与$$X$$的乘积：$$aX$$或$$AX+d$$满足的分布：$$(au, a^T\Sigma a)$$或$$(Au+d, A\Sigma A^T)$$.

&emsp; 对于多个多元正态分布：$$X_i \sim N_P(\mu_i, \Sigma_i)$$，$$k_iX_i \sim (\sum_{i}k_i \mu_i, \sum_i k_i^2\Sigma_i )$$

&emsp; **条件分布**。对于多元正态分布，假设其维度为p，如果已知其中部分维度（比如后p-q维）已知，求剩下未知维度的分布，叫做条件分布。首先，前q维称作$$X^{(1)}$$，后p-q维称为$$X^{(2)}$$，如果要求$$P(X^{(1)}\mid X^{(2)})$$的分布，则第一步要对$$u$$和$$\Sigma$$进行分割：

$$\mu= 

\begin{bmatrix}
   u^{(1)} \\
   u^{(2)}
\end{bmatrix} 
\\
\Sigma = 
\begin{bmatrix}
   \Sigma_{11} & \Sigma_{12}\\
   \Sigma_{21} & \Sigma_{22}
\end{bmatrix} 

$$

&emsp; 之后，可以给出条件分布的计算公式：

$$u_{1,2} = u^{(1)} + \Sigma_{12}\Sigma_{22}^{-1}(x^{(2)} - u^{(2)})\\
\Sigma_{11,2} = \Sigma_{11} - \Sigma_{12}\Sigma_{22}^{-1}\Sigma_{21}
$$

&emsp; **偏相关系数**则可以在条件概率分布的基础上进行计算，比如$$X = (x_1,x_2,x_3) \sim N_3(u, \Sigma)$$，已经计算出来$$P((x_1,x_2)\mid x_3)$$的条件概率的协方差矩阵为：

$$\begin{bmatrix}
\sigma_{11.3} & \sigma_{12.3}\\
\sigma_{21.3} & \sigma_{22.3}
\end{bmatrix}
$$ 

&emsp; 则，在$$x_3$$已知的情况下，$$(x_1,x_2)$$的偏相关系数为：

$$\frac{\sigma_{12.3}}{\sqrt{\sigma_{11.3} \sigma_{22.3}}}
$$ 

&emsp; **复相关系数**，一般用来衡量第一个变量与剩余变量的线性函数的最大相关系数，由于q=1，所以之前的分割符号稍作修改：

$$
\Sigma = 
\begin{bmatrix}
   \sigma_{11} & \sigma_{12}^T\\
   \sigma_{21} & \Sigma_{22}
\end{bmatrix} 

$$

&emsp; 然后给出复相关系数的计算：
$$\sqrt{\frac{\sigma_{12}^T \Sigma_{22}^{-1} \sigma_{12}}{\sigma_{11}}}$$

&emsp; **边际分布**，是指对于多维随机变量，只包含其中部分变量的概率分布（比如对于p维多元正态分布，其前m个维度的边际分布，为直接忽略后p-m个维度）。比如对于随机变量$$P(X_1, X_2)$$：

$$P(X_1) = P(X_1 \mid X_2) = \sum_i P(X_1 \mid X_{2i})$$

#### 2.2 多元正态分布的参数估计与假设检验

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

