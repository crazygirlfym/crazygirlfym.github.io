---
layout: post
title: 关于矩阵乘法的那点事
date: 2018-11-08 17:21:24.000000000 +09:00
tags: numpy, tensorflow 
---

不知道你们在用numpy或者tensorflow的时候有没有总是一种苦恼，就是分不清楚点乘，乘法以及内积外积。发现之前都是瞎搞，所以现在闲下来想要彻底搞清楚下。

内容List:
* 内积与外积
* 点乘和矩阵乘法
* numpy和tensorflow中的使用


### 内积与外积

内积：一个行向量乘以一个列向量称作向量的内积，又叫做点积，结果是一个数。

外积：一个列向量乘以一个行向量称作向量的外积，一种特殊的克罗内克积，结果是一个矩阵。

向量的点乘也叫向量的内积，是对两个向量执行点乘运算，就是对两个向量对应位一一相乘之后求和的操作，点乘的结果是一个标量。它的几何意义是可以用来表征或计算两个向量之间的夹角，以及在向量上的投影。  
向量的外积是一个向量，并且两个向量的外积与这两个向量组成的坐标平面垂直。它的物理意义是，在二维集合中，向量a和b的结果是向量a和b构成的平行四边形的面积。三维几何中，向量a和向量b的叉乘结果是一个向量，该向量垂直于a和b向量构成的平面，即法向量。


### 矩阵乘法

内积和外积一般是针对向量来讨论的。从向量的角度来讨论下矩阵乘法：
此时AB为两个新的向量的外积形式，按照外积的定义，这里$$a_i, b_j$$都是向量，因此$$a_i^Tb-j$$就是一个内积，矩阵的乘积是两个向量的外积，并且外积矩阵中每一个元素是一个内积。


### Numpy和Tensorflow中的使用

* np.multiply()：对向量或矩阵执行对应位置相乘，输出与相乘向量和矩阵的大小一致

```
	A = np.asarray([[2, 2], [1, 4]])
	B = np.asarray([[1, 1,], [2, 1]])
	print np.multiply(A, B )

	### out 
	### [[2 2]
	### [2 4]]

```
* np.dot：对向量执行内积，对秩不为1的二维数组，执行矩阵乘法运算。

```
	C = np.asarray([1, 2])
	D = np.asarray([2, 2])
	print C.dot(D)  ## 6

	A = np.asarray([[2, 2], [1, 4]])
	B = np.asarray([[1, 1,], [2, 1]])
	print A.dot(B)   

	## out
	## [[6 4]
	## [9 5]]
```

* *乘法，需要特别注意，当AB为array的计算的是数量积，类似于np.multiple(), 当AB为matrix的时候计算的是矢量积，类似于np.dot()

```
	A = np.asarray([[2, 2], [1, 4]])
	B = np.asarray([[1, 1,], [2, 1]])
	print A * B 

	### out 
	### [[2 2]
	### [2 4]]


	A = np.mat([[2, 2], [1, 4]])
	B = np.mat([[1, 1,], [2, 1]])

	print A * B

	## out
	## [[6 4]
	## [9 5]]
```

* np.tensordot() 我的理解是该方法适用于我们在计算神经网络的时候，一般输入是batch输入的x, weight矩阵是同一个，因此一个B * M* k  和 k*h的指定axes=1，结果为B * M* h。
```
	import numpy as np
	a = np.array([[[3, 4], [1, 2]], [[1, 3], [1, 1]],])   ## 2* 2 *2
	b = np.array([[2],[1]])
	print a.shape  ## (2, 2, 2)
	print b.shape ## (2, 1)

	## out [[[10][ 4]][[ 5][ 3]]]

	print np.tensordot(a, b, axes=1)  ##batch tensordot
```

* tf.multiple() 对应np.multiple()
* tf.matmul(A, B) 对应np.dot() 如果出现三维张量的情况，选择A最外围的两维度和B最内的两个维度，其他维必须是相同的，可以实现batch的乘法操作。

```

	import tensorflow as tf

	a = tf.constant([[[1, 2], [2,3]], [[1,1], [1,2]]])
	b =  tf.constant(([[[1, 2], [2,3]]]))
	sess = tf.Session()
	with sess.as_default():
		print(sess.run(tf.matmul(a, a)))  
		# out
		## [[[ 5  8]
		 ##  [ 8 13]]
		 ##[[ 2  3]
		 ##[ 3  5]]]

    
```
* tf.tensordot() 对应np.tensordot()
