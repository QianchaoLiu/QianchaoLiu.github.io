---
layout: post
title:  "Recommender System"
date:   2016-04-31 10:00:04
categories: liuqianchao update
---

&nbsp;&nbsp;&nbsp;&nbsp;近些年来，提到推荐系统，我们更多的是指个性化推荐系统，而个性化推荐系统是较大众化推荐而言的。   
&nbsp;&nbsp;&nbsp;&nbsp;大众化推荐经常被用于检索推荐，比如在京东网购时，按照商品“综合排序”展示检索结果，就属于大众化推荐，当然大众化推荐也经常与个性化推荐搭配使用。本文将对个性化推荐展开介绍，大众化推荐不属于本文范畴。   
&nbsp;&nbsp;&nbsp;&nbsp;本文根据推荐方法的思想不同，主要介绍了基于人口统计学的推荐等方法。


### 1.基于人口统计学的推荐
&nbsp;&nbsp;&nbsp;&nbsp;基于人口统计学的方法(Demographic-Based Recommendation)的思想源自“相似的用户有着相似的爱好”。该方法通过计算系统中用户的相似度，相似度的依据主要是指性别、年龄、工作等，而不包括对商品的评分、购买记录（该方法特指基于用户的协同过滤方法），对a用户推荐与他相似的b用户喜欢的商品。   
&nbsp;&nbsp;&nbsp;&nbsp;该方法的特点是，推荐思想简单明了，主要的计算来自对用户相似度的计算，且用户的相似度计算方法十分简单，常用的方法包括欧式距离、余弦相似度等。应用场景十分广泛，京东在其“用户画像”技术中，常用的推荐方法就是该方法，除此之外，该方法用多的应用是与其他的推荐方法相结合。    
&nbsp;&nbsp;&nbsp;&nbsp;用户$$X_{1}$$,$$X_{2}$$的欧式距离的计算示例如下:   

<div align="center">$$S(X_{1},X_{2}) = \sum_{i=1}^N (x_{1i}-x_{2i})^2 $$</div>

### 2.基于内容的推荐
&nbsp;&nbsp;&nbsp;&nbsp;和上一个方法十分相似，只不过是假设产生了变化，基于内容的推荐(Content-Based Recommendation)是假设“用户喜欢他之前喜欢的东西“。比如我玩过魔兽，炉石，系统就会为给推荐新出的游戏“守望先锋”。   
&nbsp;&nbsp;&nbsp;&nbsp;基于内容的推荐的计算过程大致可以划分为两类，一类是直接计算商品间的相似性，按照相似性由高到低排序进行推荐；另一类是通过构建用户画像，将用户浏览过、购买过的商品的属性作为用户的画像，这个画像描述了用户对于商品属性的偏好特征，然后计算待推荐商品与用户画像之间的相似度，进行推荐。   
&nbsp;&nbsp;&nbsp;&nbsp;基于内容的推荐方法有一个优点就是能很好的解决冷启动(Cold－Start)的问题。冷启动是指新产品的推出时如何进行推荐的问题，之前的一些方法的推荐往往是通过该商品的历史评分、历史购买记录进行的，基于内容的推荐则解决该问题。继续拿“守望先锋”来举例，我可以通过TF-IDF来提取该游戏商品介绍文本中的关键词，提取到“暴雪”等字段，根据该“属性”就可以推荐给暴雪系游戏的玩家。   
&nbsp;&nbsp;&nbsp;&nbsp;同时需要注意的是，由于要计算商品之间的相似性，所以主要需要需要维护商品到商品属性的矩阵，比如对于一款游戏商品，其商品属性包括游戏类型、出品商等属性字段，因此对于不同类别的的商品需要设计不同的商品属性字段。

### 3.基于协同过滤的推荐
&nbsp;&nbsp;&nbsp;&nbsp;基于协同过滤的推荐(Collaborative Filtering-Based Recommendation)，又可以细分为基于用户的(User-based),基于物品的(Item-based)以及基于模型的(Model-based)。   
&nbsp;&nbsp;&nbsp;&nbsp;基于用户的协同过滤，与上文中提到的基于人口统计的推荐不同之处的计算用户相似时用的是用户对于商品的评分或者购买浏览记录，而不是人口统计特征。基于用户的协同过滤维护这样一个$$m × k$$的矩阵$$M$$.其中有$$m$$个用户和$$k$$个商品,通过计算行与行间的  

<div id="content">

    <table cellspacing="0">
    <tr><th></th><td>item1</td><td>item2</td><td>item3</td></tr>
    <tr><td>user1</td><td>3</td><td>3</td><td></td></tr>
    <tr><td>user2</td><td>1</td><td>1</td><td></td></tr>
    <tr><td>user3</td><td></td><td></td><td>5</td></tr>

    </table>

</div> 
<div align="center">基于用户的协同过滤</div>

&nbsp;&nbsp;&nbsp;&nbsp;基于物品的协同过滤，与上文中提到的基于内容的推荐比较相似，不同之处是相似物品的的计算不是通过商品的属性，而是通过网络中用户对商品的历史浏览记录。   
&nbsp;&nbsp;&nbsp;&nbsp;如果用户同时浏览过item1和item2，则下表中(1,2)和(2,1)处的值为3，这样可以表征商品之间的关联相似性，上述矩阵是通过$$M^{-1}M$$的到的，$$M$$是基于用户的协同过滤维护的矩阵。


<div id="content">

    <table cellspacing="0">
    <tr><th></th><td>item1</td><td>item2</td><td>item3</td></tr>
    <tr><td>item1</td><td></td><td>3</td><td></td></tr>
    <tr><td>item2</td><td>3</td><td></td><td></td></tr>
    <tr><td>item3</td><td></td><td></td><td></td></tr>

    </table>

</div> 
<div align="center">基于物品的协同过滤</div>

#### Reference
1. Zhiyuan Liu [Big Data Intelligence](http://www.amazon.com/s/ref=nb_sb_noss?url=search-alias%3Daps&field-keywords=big+data+intelligence&rh=i%3Aaps%2Ck%3Abig+data+intelligence)

