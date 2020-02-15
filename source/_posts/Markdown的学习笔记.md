---
title: Markdown的学习笔记
date: 2019-05-27 17:55:36
tags: Markdown
categories: Markdown
---



# Markdown的学习笔记

参考自：[MarkDown-语法说明](http://www.markdown.cn/ "教程-MarkDown")

<!-- 文章中的参考式链接管理 -->

[myBlog]:  https://forhcl.github.io/  "Hogan_Lee&#39;note"

#### Markdown介绍

一个Web上使用的文本到HTML的转换工具，可以通过简单、易读易写的文本格式生成结构化的HTML文档。

#### Markdown和HTML的区别？

<u>**HTML 是一种发布的格式，Markdown 是一种书写的格式。**</u>

也就是说Markdown到最后还是要装换成HTML才能呈现。那为什么还会有Markdown的出现呢？而且为什么会这么受人欢迎呢？那肯定是它有优于HTML的闪光点！！

1. **Markdown只对应HTML的一小部分标记。（即纯文本可以涵盖的范围）**
当然会有程序员朋友觉得HTML的标记不难记，但是对于非程序员的用户他们的<u>关注点在于撰写的内容上</u>，并不想花费太多的时间去学习HTMl标记的语义，这时候Markdown的语法简单，但是学习成本低，容易上手。又能达到他们想要的效果，何乐不为呢！！
   
2. 那问题又来了，Markdown对应的HTML标记那么少，有一些功能不能实现，需要用到Markdown涵盖范围之外的HTML标记呢？不用担心，**Markdown兼容HTML,在Markdown环境下编写的HTML5代码也能实时转换成对应的格式。**<u>不在Markdown涵盖范围之内的标签，都可以直接在文档里面用HTML撰写。当然如果你乐意，在Markdown涵盖范围内的标签也可以直接用HTML标记来撰写。不过这就没意思啦</u>

   注意：在HTML区块标签间的Markdown格式语法将不会被处理。

3. **撰写过程中的直观性。**不会像是由许多标签或是格式指令所构成，关注于内容本身，没有HTML的各种标签的干扰。排版格式良好、可

4. 可读性强。

   *Markdown也是存在一些缺陷的，具体可以自己自行上网搜索。*

#### Markdown的语法

##### 区段元素

###### 链接

1. 行内式

   ```markdown
   格式：
   [链接文字](网址链接 "Title")
   例：
   [我的博客](https://forhcl.github.io/ "Hogan_Lee'note")
   ```

   效果展示：[我的博客](https://forhcl.github.io/ "Hogan_Lee'note")

   注意：`"Title"`是可选的部分，对应**HTML5**的`<a>`标签的`title`属性。当写入`"Title"`后，鼠标在超链接上悬停时会把`Title`显示出来。一般将`Title`设置为链接对应网页的标题。

2. 参考式

   ```markdown
   格式：
   [链接文字][辨识链接的标记]
   然后，在文件的任意处，定义标记的链接内容
   [标记]: 网址链接  "Title"
   [标记]: 网址链接  'Title'
   [标记]: 网址链接  (Title)
   例子：
   [我的博客][myBlog]
   [myBlog]: https://forhcl.github.io/ "Hogan_Lee'note"
   ```

   效果展示：[我的博客][myBlog]

   __隐式链接标记功能__让你可以省略指定链接标记，这种情形下，链接标记会视为等同于链接文字，要用隐式链接标记只要在链接文字后面加上一个空的方括号，如果你要让 "Google" 链接到 google.com，你可以简化成：

   ```markdown
   [Google][]
   ```

   然后定义链接内容：

   ```markdown
   [Google]: http://google.com/
   ```

    注意：

   + `Title` 依旧是可选的，可以用单引号、双引号或是括弧包着，三种标记的链接内容的定义都是相同的。当然也可以把title属性放到下一行。

   + 辨识链接的标记不区分大小写。

   __行内式和参考式的比较__：对于这两种定义链接的语法，给我最直观的感觉就是参考式对于链接的管理十分便捷。

   ###### 图片

   类似于链接的格式

   1. 行内式

      ```markdown
      ![Alt text](/path/to/img.jpg "Optional title")
      ```

   2. 参考式

      ```md
      ![Alt text][id]
      ```

      「id」是图片参考的名称，图片参考的定义方式则和连结参考一样：

      ```md
      [id]: url/to/image  "Optional title attribute"
      ```

   注意：到目前为止， Markdown 还没有办法指定图片的宽高，如果你需要的话，你可以使用普通的` <img> `标签。