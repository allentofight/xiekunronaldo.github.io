---
layout: post
title: "Head First C第七章笔记-高级函数"
date: 2014-07-15 22:11:00 +0800
comments: true
categories: 
---
导读:这一章我们将学习函数的一些高级特性,如

1. 将函数作为一个参数进行传递
2. 用比较函数进行排序
3. 用可变参数的函数让你的代码变得更加灵活


#####函数指针

	int (*warp_fn)(int);

将函数指针当作参数传递给函数

```
void find(int (*mactch)(char *)){
	if (match(ADS[i])){
		//char *ADS[] = {"one", "two"};
	}
}
```

#####用函数指针来排序
C标准库里有一个排序指针如下

{% img /images/2014/7/qsort.png%}

假设你有以下整形数组，你想按升序排序，该如何写排序函数 

```
int scores[] = {543,323,32,554,11,3,112};
int compare_scores(const void* score_a, const void* score_b)
{
	int a = *(int*)score_a; 
	int b = *(int*)score_b;
	return a-b;
}
qsort(scores, 7, sizeof(int), compare_scores);
```
如果是一个char *p[]数组呢，该如何写compare函数

```
int compare_names(const void* a, const void* b){
	char** sa = (char**)a;
	char** sb = (char**)b; 
	return strcmp(*sa, *sb);￼￼￼￼￼￼￼￼
}
```
