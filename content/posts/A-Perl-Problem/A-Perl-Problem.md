+++
title = "A Perl Problem"
date = 2016-01-04
tags = ["code"]
categories = [""]
draft = false
+++
前阵寝室的生物学博士来找我，问我会不会编程，我说还好啊，要看什么语言了，有的语言也不熟悉，他说其实很简单的，就是处理一些数据。
<!--more-->
需求是这样的：

1. 要取出所有 x:x 类型的数据
2. 不取 0:x 或者 x:0 这样的数据
3. 计数

看到 1 和 2 首先想到的肯定是正则表达式了，我想这其实也不难，用 python 几行也就写了，实在不行上网找一个处理字符串的程序改一改也就ok了，然而博士说要用 perl 写，顿时慌了，perl 只闻其名未见其人...但是据说是一种具有各种功能的脚本语言，很重要的一点是内部集成<b>正则表达式</b>。虽然没接触过，硬着头皮上，只能简单学一下了，毕竟浓浓室友情。最后搞了俩小时搞出来了，得到的程序是这样的：

```perl
#!/usr/bin/perl

use strict;
use warnings;
my $filename_in = $ARGV[0];     #chicken_SNPs.txt
my $filename_out = $ARGV[1];
open(IN,$filename_in);
open(OUT,">$filename_out");

my @head;
my %hash;
my $popname;
my @array;

while(<IN>){
chomp;
	if($_=~/^chr/){
		@head=split(/\t/,$_);
	}else{
		@array=split(/\t/,$_);
		for (my $a=5;$a<15;$a++){
			$popname=$1 if($head[$a]=~/pop(\S+)/);
			push @{$hash{$popname}},$array[$a];
		}
	}
}


my %hash2;
foreach my $pop (keys %hash){
	for my $data (@{$hash{$pop}}){
		if($data!~/0\:/ and $data!~/\:0/){
			$hash2{$pop}++;
		}
	}
}

foreach my $pop (sort {$a <=> $b} keys %hash2){
	print OUT "pop$pop\t$hash2{$pop}\n";
}

```
### 大概思路及收获

**思路**

对于这种处理文件的问题，首先当然是读文件，而如何读，读到哪，读取哪些部分就成了我们要考虑的问题，对于我们要解决的问题，大概方法就是

> 按行读，去掉tab，取相关的字符串

所以首先需要变量去存取读出来的字符串，在我们的程序中是“@array”和“@head”，而“@head”是第一行的内容，用以区分数据列，此步完成了需求1。存储结束后便要对取出的字符串进行筛选，这一步便是为了完成需求2。对于匹配字符串的需求首选是正则表达式而perl语言内部就集成了对正则表达式的支持，这就让这一步变得更加容易。
