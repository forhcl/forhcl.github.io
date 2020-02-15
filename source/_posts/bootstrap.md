---
title: bootstrap
date: 2019-08-09 14:28:06
tags:
categories: Bootstrap
---

### _集成Bootstrap_

+ Bootstrap简介、核心概念

  - 基于HTML、CSS、JavaScript的前端框架

  - 响应式布局

  - 移动设备优先

  - 遵循html5规范(如果不是html5可能会出现一系列的问题)

  - 响应式meta标签

    ```html
    <meta name="viewport" content="width=device-width,initial-scale=1,shrink-to-fit=no">
    ```

  - Normalize.css

    - 使用Normalize来建立跨浏览器的一致性
    - Reboot

+ Bootstrap网格系统

  - 基础的CSS是移动优先。优先设计更小的宽度

  - 媒体查询。针对与平板电脑、台式电脑

  - 渐进增强。随着屏幕大小的增加而增加元素。

  - 响应式：viewport尺寸的增加，系统会自动分为最多12列，根据屏幕宽度进行响应。

    ![](bootstrap/bootstrap网格选项.png)

+ Bootstrap网格示例

  移动设备及桌面设备`.col-xs-*`、`.col-md-*`

  ```html
  <!-- Stack the columns on mobile one full-width and the other half-width -->
  <div class="raw">
      <div class="col-xs-12 col-md-8">
          .col-xs-12 .col-md-8
      </div>
      <div class="col-xs-6 col-md-4">
          .col-xs-6 .col-md-4
      </div>
  </div>
  ```

  

+ Bootstrap与Spring Boot集成
+ Bootstrap实战

剧情：引入样式表，套样式。















### _Bootstrap与Spring Boot集成_

##### _配置环境_

+ Tether 1.4.0：<http://tether.io/>

  Bootstrap中的一些样式依赖于Tether来实现的

+ Bootstrap v4.0.0-alpha.6：<https://v4-alpha.getbootstrap.com>

+ JQuery 3.1.1:<http://jquery.com/download/>

常用前端框架与Spring Boot集成

+ Font Awesome 4.7.0：<http://fontawesome.io> 图标库
+ NProgress 0.2.0:<http://ricostacruz.com/nprogress/> 进度条
+ Thinker-md：<http://git.oschina.net/benhail/thinker-md> 使编辑器支持MarkDown语法
+ JQuery Tags Input 1.3.6：<http://xoxco.com/projects/code/tagsinput/>
+ Bootstrap Chosen 1.0.3：<https://github.com/haubek/bootstrap4c-chosen>
+ toastr 2.1.1：<http://www.toastrjs.com> 响应处理如出错警示

下载下来放到static包里面，

css一般在head中引入，js在body的底部进行引入，js较大最后加载对性能优化较好

css放在header中，js放在footer里面