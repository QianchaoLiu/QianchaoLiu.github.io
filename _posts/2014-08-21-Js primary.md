---
layout: post
title:  "Js Primary"
date:   2014-08-21 15:49:04
categories: liuqianchao update
---
JavaScript
----------
导入js



```
\<script type="text/javascript">
</script>
or
\<script src="">
</script>
js代码的存放位置：可以放在html的任何位置，好的习惯是放在head或body
```  

 

注释

```
/*
多行注释
*/

//单行注释
```


变量

```var 变量名区分大小写```



逻辑判断  

```
if(条件)｛｝  
else{}
```
函数

```
function 函数名()
{

}

函数的调用
<input type="button" onclick="函数名()">
</input>
```

几个常用的js函数

```
document.write();//直接向html输出文本
alter();//警告，出现弹框，包含一个确定按钮
confirm();//confirm消息对话框，有一个返回值为true 或 false
prompt();//包含确定、取消、文本输入框，返回值为string
window.open(url,<窗口名称>,<参数字符串>)//返回一个窗口对象
window.close();或窗口对象.close();//关闭当前窗口，
```

DOM

```
//获取元素：
Object=document.getElementById("ID")//通过id获取元素
//获取或替换html元素的内容
Object.innerHTML
//改变html元素的样式
Object.style.color;Object.style.backgroundColor;
Object.style.display="block"or"none";
//为元素改变css样式
Object.className
```