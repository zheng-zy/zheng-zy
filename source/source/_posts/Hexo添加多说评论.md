---
title: Hexo添加多说评论
description: Hexo添加多说评论
categories: 技术
tags: Hexo
abbrlink: 57607
date: 2016-09-21 13:49:09
---
<!-- more -->
### 申请[多说](http://duoshuo.com/create-site/)账号
### 创建站点，填写相关信息
![这里写图片描述](http://odr4ba8oz.bkt.clouddn.com/image/hexo/5.png)
### 设置——>自定义css样式

```
/*Head Start*/
#ds-thread #ds-reset ul.ds-comments-tabs li.ds-tab a.ds-current {
    border: 0px;
    color: #6D6D6B;
    text-shadow: none;
    background: #F3F3F3;
}

#ds-thread #ds-reset .ds-highlight {
    font-family: Microsoft YaHei, "Helvetica Neue", Helvetica, Arial, Sans-serif;
    ;font-size: 100%;
    color: #6D6D6B !important;
}

#ds-thread #ds-reset ul.ds-comments-tabs li.ds-tab a.ds-current:hover {
    color: #696a52;
    background: #F2F2F2;
}

#ds-thread #ds-reset a.ds-highlight:hover {
    color: #696a52 !important;
}

#ds-thread {
    padding-left: 15px;
}

#ds-thread #ds-reset li.ds-post,#ds-thread #ds-reset #ds-hot-posts {
    overflow: visible;
}

#ds-thread #ds-reset .ds-post-self {
    padding: 10px 0 10px 10px;
}

#ds-thread #ds-reset li.ds-post,#ds-thread #ds-reset .ds-post-self {
    border: 0 !important;
}

#ds-reset .ds-avatar, #ds-thread #ds-reset ul.ds-children .ds-avatar {
    top: 15px;
    left: -20px;
    padding: 5px;
    width: 36px;
    height: 36px;
    box-shadow: -1px 0 1px rgba(0,0,0,.15) inset;
    border-radius: 46px;
    background: #FAFAFA;
}

#ds-thread .ds-avatar a {
    display: inline-block;
    padding: 1px;
    width: 32px;
    height: 32px;
    border: 1px solid #b9baa6;
    border-radius: 50%;
    background-color: #fff !important;
}

#ds-thread .ds-avatar a:hover {
}

#ds-thread .ds-avatar > img {
    margin: 2px 0 0 2px;
}

#ds-thread #ds-reset .ds-replybox {
    box-shadow: none;
}

#ds-thread #ds-reset ul.ds-children .ds-replybox.ds-inline-replybox a.ds-avatar,
#ds-reset .ds-replybox.ds-inline-replybox a.ds-avatar {
    left: 0;
    top: 0;
    padding: 0;
    width: 32px !important;
    height: 32px !important;
    background: none;
    box-shadow: none;
}

#ds-reset .ds-replybox.ds-inline-replybox a.ds-avatar img {
    width: 32px !important;
    height: 32px !important;
    border-radius: 50%;
}

#ds-reset .ds-replybox a.ds-avatar,
#ds-reset .ds-replybox .ds-avatar img {
    padding: 0;
    width: 32px !important;
    height: 32px !important;
    border-radius: 5px;
}

#ds-reset .ds-avatar img {
    width: 32px !important;
    height: 32px !important;
    border-radius: 32px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.22);
    -webkit-transition: .8s all ease-in-out;
    -moz-transition: .4s all ease-in-out;
    -o-transition: .4s all ease-in-out;
    -ms-transition: .4s all ease-in-out;
    transition: .4s all ease-in-out;
}

.ds-post-self:hover .ds-avatar img {
    -webkit-transform: rotateX(360deg);
    -moz-transform: rotate(360deg);
    -o-transform: rotate(360deg);
    -ms-transform: rotate(360deg);
    transform: rotate(360deg);
}

#ds-thread #ds-reset .ds-comment-body {
    -webkit-transition-delay: initial;
    -webkit-transition-duration: 0.4s;
    -webkit-transition-property: all;
    -webkit-transition-timing-function: initial;
    background: #F7F7F7;
    padding: 15px 15px 15px 47px;
    border-radius: 5px;
    box-shadow: #B8B9B9 0 1px 3px;
    border: white 1px solid;
}

#ds-thread #ds-reset ul.ds-children .ds-comment-body {
    padding-left: 15px;
}

#ds-thread #ds-reset .ds-comment-body p {
    color: #787968;
}

#ds-thread #ds-reset .ds-comments {
    border-bottom: 0px;
}

#ds-thread #ds-reset .ds-powered-by {
    display: none;
}

#ds-thread #ds-reset .ds-comments a.ds-user-name {
    font-weight: normal;
    color: #3D3D3D !important;
}

#ds-thread #ds-reset .ds-comments a.ds-user-name:hover {
    color: #D32 !important;
}

#ds-thread #ds-reset #ds-bubble {
    display: none !important;
}

#ds-thread #ds-reset #ds-hot-posts {
    border: 0;
}

#ds-reset #ds-hot-posts .ds-gradient-bg {
    background: none;
}

#ds-thread #ds-reset .ds-comment-body:hover {
    background-color: #F1F1F1;
    -webkit-transition-delay: initial;
    -webkit-transition-duration: 0.4s;
    -webkit-transition-property: all;
    -webkit-transition-timing-function: initial;
}
/*Head End*/
```
### 主题配置文件启用多说
```
duoshuo_shortname: zheng-zy # 多说域名
```
### 访问查看[zheng-zy.github.io](http://zheng-zy.github.io/)即可
