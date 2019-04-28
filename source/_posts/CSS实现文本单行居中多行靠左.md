---
title: CSS 实现文本单行居中，多行靠左
date: 2019-03-31 11:06:35
category: CSS
---

实现文本单行居中，多行靠左利用了`display: inline-block`的特性。外边使用一个块级元素，并设置居中对齐，这样的话，块级元素内的行内元素是会居中对齐的。里边使用一个行内块级元素，整个行内块级元素会相对于外层的`div`呈现出行内元素的特性，因此会居中。然后设置自身文本靠左对齐。多行的情况下由于文本撑满了外部的容器，因此看上去是靠左的，实际上一直都是相对于容器居中。talk is cheap，请看下面代码

```
// html
<div>
 <span>这里是文字</span>   
<div>

// css
div {
  text-align: center;
}

span {
  display: inline-block;
  text-align: left;
}

```


