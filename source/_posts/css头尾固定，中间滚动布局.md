---
title: css头尾固定，中间滚动布局
date: 2017-12-05 10:47:31
tags: css
---
	html,body{
		height:100%;
	}
<!--more-->
	body{
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}
	/*
	*tBox,顶部盒子，
	*bBox，尾部盒子
	*cBox，中间盒子
	*/
	.tBox,.bBox{
		flex-grow: 0;
		flex-shrink:0;
	}
	.cBox{
		flex-grow: 1;
		overflow: auto;
	}