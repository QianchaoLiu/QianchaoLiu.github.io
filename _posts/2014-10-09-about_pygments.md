---
layout: post
title:  "About pygments"
date:   2014-10-09 22:35:04
categories: liuqianchao update
---

Highlight for your code by pygments.

###1. About pygments
**pygments** is a syntax-highlighting package and Python-based code highlighting tool. With it, can highlight our codes in our website.

###2. How to intall it:

- Step 1, intall **pygments**:   

```
sudo easy_install pygments.
```   
to make sure that your have installed python and easy_intall in your pc   
 
- Step 2, get **.css** file :   

```
pygmentize -f html -S default > pygments.css
```

- Step 3, set your jekyll.   
  copy this .css file to your local files of jekyll:`./css`,   
  add `<link rel="stylesheet" href="/css/pygments.css">` to your `head.html`.   
  and add `highlighter: pygments`to your `_config.yml`   

  
###3. Result:   


{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}

