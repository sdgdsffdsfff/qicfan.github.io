---
author: qicfan
comments: true
date: 2012-02-10 08:48:33+00:00
layout: post
slug: hessian-for-php%e7%bb%ad
title: Hessian for php续
wordpress_id: 376
categories:
- php
tags:
- Hessian
- HessianPHP
- php
- typeMap
---

前面发过一篇Hessian for php的使用记录，后续又有一些地方用到了Hessian并且发现了一些心的问题，如下：

Java方提供了一个接口，返回值是一个java对象类似：com.project.xxx.xxx.class1，然后在php中调用这个借口，会报错误，找不到这个com.project.xxx.xxx.class1这个对象，由于php中对象名不可能出现'.'，所以不能直接定义com.project.xxx.xxx.class1这个对象或者class1这个对象，必须要用到typeMap进行映射，方法如下：

    
    $handler = new HessianClient ( $this->getUrl (), array ('version' => 1, 'saveRaw' => TRUE, 'typeMap' => array('class1' => 'com.project.xxx.xxx.class1') ) );


如上代码只是再实例化时在$options参数中增加一项typeMap即可，typeMap是个数组，其中的结构为local=>remote，本地对象名称为键，远程对象名称为值
设定好这个参数后，本来应该可以用了，但是在我的环境中调试时依然报找不到对象的错误，所以我就检查了下源码，发现在HessianTypeMap.php中的HessianTypeMap->getLocalType()方法中前两行为：

    
    
    if(class_exists($remoteType))
        return $remoteType;
    


我的环境中，class都会使用自动加载，所以如果有这个判断就会报告对象找不到（根本不存在），所以我的解决方法就是注释掉这两行，当然看环境和情况而定，这个代码在正常环境下应该没啥问题，但是一些OO的框架可能就会报错。

