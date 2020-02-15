---
title: Blog questions
date: 2019-06-04 23:26:51
tags: Hexo
categories: Hexo
---

1. 正常的`MarkDown`语法下图片部署装换成HTML后无法显示

参考文档：[资源文件夹](https://hexo.io/zh-cn/docs/asset-folders)

原因：部署以后，图片会自动添加日期相关的文件结构目录

{% asset_img image-1.png %}

解决方案：

+ 将站点配置文件 `config.yml` 文件中的 `post_asset_folder` 选项设为 `true`：开启文章资源文件夹

+ 在插入图片的时候不使用`MarkDown`语法（可能造成图片或其他资源显示不正确），而使用`Hexo 3`提供的相对路径引用的标签插件。

+ 缺陷：因为不是`MarkDown`语法所以在编写文章的时候没办法预览。

  ```Hexo
  {% asset_path slug %}
  {% asset_img slug [title] %}
  {% asset_link slug [title] %}
  ```

其他解决方案：可以利用插件`hexo-asset-image`实现`MarkDown`语法插入相对路径的图片。尝试多次以配置失败告终，若感兴趣可自行搜索。

2. 在hexo中使用plantUML画图，要将(当然首先要下载插件配置好，自行搜索)

   ```
   @startuml
   @enduml
   ```

   改为

   ```
   {% plantuml %}
   {% endplantuml %}
   ```

3. 如何在`MarkDown`文档中实现首行缩进？

日常写文章的时候都习惯性地每一个段落进行首行缩进，但是在MarkDown语法中却似乎没有内置段落首行缩进。这让我感到十分困扰。我尝试过用`HTML`和`CSS`来实现，如`<p style="text-indent:2em"></p>`，但是在`HTML`块中没办法使用`MarkDown`语法进行加粗、下划线等操作。可以通过使用占位符来实现首行缩进：

```
&ensp;或&#8194; //半角
&emsp;或&#8195; //全角   强烈推荐
&nbsp;或&#160;
```



