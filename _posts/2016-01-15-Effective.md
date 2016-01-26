---
layout: post
title:  "Effective Python"
date:   2016-01-15 10:00:04
categories: liuqianchao update
---


How to modify your Python code style, and use it in a more effective way, this post will introduce the pythonic code style to you.

####Language sugar and suggested code style

- **slice**   
	You may see [::-1] in python, you also may see [begin:end], which is a brief edition of [begin:end:step]. [begin:end] is suggested, and [begin:end:step] often causes unexpected bugs, for example, it will break for Unicode characters

- **str.format()**    
	In C++ ect., %s is used to express a string, it works for python, but the suggested way to express string in python is:
	{% highlight python %}
	value={'greet':'Hello world','language':'Python'}
	print '%(greet)s from %(language)s.' % value
	#or
	print '{greet} from {language}.'.format(greet='Hello world',language='Python')
	{% endhighlight %} 

- **join**   
	{% highlight python %}
	''.join(['str1','str2']) #instead of using str1+str2.
	{% endhighlight %} 

- **with**   
	To close a file immediately, use 'with' to operate the file.
	{% highlight python %}
 	with open('files/tex.txt') as f:
 		f.write('first line')
	{% endhighlight %} 

- **zip**
	The zip() built-in function can be used to iterate over multiple iterators in parallel. And in Python 2, zip returns the full result as a list of tuples.
	{% highlight python %}
	x=[1,2,3]
	y=[4,5,6,7]
	zip(x,y) #[(1,4),(2,5),(3,6)]
	{% endhighlight %} 	

- **assert**   
	use assert expression1,expression2 to capture the constraint defined by the user. 

- **deepcopy**   
	Distinguish shallow copy from deep copy and '='
	{% highlight python %}
	#= reference
	import copy
	copy.copy(object)#shallow copy, one time of coping
	copy.deepcopy(object)#deep copy, copy objects iteratively
	{% endhighlight %} 	

- **map**   
	use `map( func, seq1[, seq2...] )` to call a function and return a list of the results, which is equal to `[f(x) for x in iterable]`.
	{% highlight python %}
	#first situation: map(func,iterable) where func only has one para
	#second situation: map(func,iterable1,iterable2) where func(para1,para2) and first elem of iteable1 and iterable2 are used as para1 and para2.
	#if the func is none, map() is equal to zip()
	#all constraint to: length of return is equal to length of iterable
	{% endhighlight %} 	


- **Concurrency and Parallelism**   
	To make full use of the CPU resource of computer. Concurrent(Thread) program may run thousands of separate paths of execution simultaneously. In contrast, the time parallelism(Process) takes to do the total work is cut in half.   
	In python, The existence of GIL(Global Interpreter Lock) means your program could utilize only one thread at the same time.   
	**CPU-bound**:Multiprocessing   
	**I/O-bound**:Use use `Threading` for blocking I/O, which may take more time to execute the CPU-bound program. 


#### Reference
1. Brett Slatkin *Effective Python*.
2. Y.Zhang, Yh.Lai *Writing Solid Python Code*.

