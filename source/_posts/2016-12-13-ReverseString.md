---
title:  "旋转字符串"
date: 2016-12-13 12:00:00
updated: 2016-12-13 12:00:00
comments: true
categories: 算法
tags:
    - PHP
    - 算法
---
## 题目描述

**给定一个字符串，要求把字符串前面的若干个字符移动到字符串的尾部，如把字符串“abcdef”前面的2个字符'a'和'b'移动到字符串的尾部，使得原字符串变成字符串“cdefab”。请写一个函数完成此功能。禁止使用字符串函数!**

<!-- more -->

## 分析与解法

将一个字符串分成X和Y两个部分，在每部分字符串上定义反转操作，如X^T，即把X的所有字符反转（如，X="abc"，那么X^T="cba"），那么就得到下面的结论：(X^TY^T)^T=YX，显然就解决了字符串的反转问题。

例如，字符串 abcdef ，若要让cdef翻转到ab的前头，只要按照下述3个步骤操作即可：

* 首先将原字符串分为两个部分，即X:ab，Y:cdef；
* 将X反转，X->X^T，即得：ab->ba；将Y反转，Y->Y^T，即得：cdef->fedc。
* 反转上述步骤得到的结果字符串X^TY^T，即反转字符串cbafed的两部分（ba和fedc）给予反转，bafedc得到cdefab，形式化表示为(X^TY^T)^T=YX，这就实现了整个反转。

~~~ php

	function ReverseString(&$s, $from, $to)
	{
		while ($from < $to) {
			$t = $s[$from];
			$s[$from++] = $s[$to];
			$s[$to--] = $t;
		}
	}

	function LeftRotateString(&$s,$n,$m)
	{
		$m %= $n;               
		ReverseString($s, 0, $m - 1);
		// echo $s; //bacdef
		ReverseString($s, $m, $n - 1);
		// echo $s; //bafedc
		ReverseString($s, 0, $n - 1);
		// echo $s; //cdefab
	}

	$String = 'abcdef';
	$n = 6;
	$m = 2;
	LeftRotateString($String,$n,$m);
	// echo $String; //cdefab
    
~~~

## 举一反三
* 链表翻转。给出一个链表和一个数k，比如，链表为1→2→3→4→5→6，k=2，则翻转后2→1→6→5→4→3，若k=3，翻转后3→2→1→6→5→4，若k=4，翻转后4→3→2→1→6→5，用程序实现。

* 编写程序，在原字符串中把字符串尾部的m个字符移动到字符串的头部，要求：长度为n的字符串操作时间复杂度为O(n)，空间复杂度为O(1)。 例如，原字符串为”Ilovebaofeng”，m=7，输出结果为：”baofengIlove”。

* 反转上述步骤得到的结果字符串X^TY^T，即反转字符串cbafed的两部分（cba和fed）给予反转，cbafed得到defabc，形式化表示为(X^TY^T)^T=YX，这就实现了整个反转。
	
