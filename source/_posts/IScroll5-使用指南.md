---
title: IScroll5 使用指南
date: 2017-12-05 10:34:37
tags: Iscroll
---
 http://blog.csdn.net/tiw163/article/details/43341759

###wrapper必须给高度，不然无法滑动。
在图片等元素未载入前可能无法确定滚动区域的长宽最好将new IScroll() 放在onload中
原生写法
<!-- more -->

	<script src="iscroll.js"><script>
        <script>
            var myscroll;
            function loaded(){
	           setTimeout(function(){
                    myscroll=new iScroll("wrapper");
	             }，100 );
	        }
       		window.addEventListener("load",loaded,false);
     </script>

或者是

	<script src="iscroll.js"><script>
        <script>
            var myscroll;
            function loaded(){
	           setTimeout(function(){
                    myscroll=new iScroll("wrapper");
	             }，100 );
	        }
         </script>
	<body onload="loaded()">
	.....
	</body>
 这种情况下iScroll会在页面资源（包括图片）加载完毕100ms之后得到初始化，这应该是一种比较安全的调用iScroll的方式。

使用一些框架的ready方法来安全调用iScroll（比如jquery里的ready()）。

jquery写法

	$(function(){
		var myscroll=new IScroll("#wrapper");
	})

或者是

	$(document).ready(function(){
		var myscroll=new IScroll("#wrapper");
	})