---
title: 富文本编辑器显示换行符
date: 2018-1-10 14:13:49
categories: tech
tags: 
    - 伪元素
    - 换行符
    - br
---



# 背景

MS在 office word工具中为使用户能够清晰知道文档的段落信息，给用户提供了显示换行符号的功能，即在当前段落的最后面显示一个回车键的箭头，如下图：





![](http://sunjingwu.oss-cn-shanghai.aliyuncs.com/content/TIM%E6%88%AA%E5%9B%BE20180110152801.png)



![](http://sunjingwu.oss-cn-shanghai.aliyuncs.com/content/TIM%E6%88%AA%E5%9B%BE20180110152854.png)





最好的方式就是通过伪元素`after`添加

```css
p.editing:after {
    content: "↵";
    color:#989898;
    font-size:14px;
    position: absolute;
    text-indent: 0;
    margin-left: 2px;
}
```



关键点：编辑器会为空的行添加`br`标签，导致换行符显示偏下一行。

合理的方案是通过事件绑定的方式，移除br 标签。
