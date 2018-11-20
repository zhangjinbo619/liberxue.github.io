---
layout: blog
tech: true
title: "react native 原生与js通信同步函数"
background-image: "http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/rn-sync-function.jpg"
date: "2018-11-20"
category: code #book,game,note,chat,code,image,web,link,design,lock
tags:
- rn
- react native
---

![rn-sync-function](http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/rn-sync-function.jpg)

# 背景
  　　在实际项目开发过程中，需要原生与js通信同步时，可使用如下方式。
# 代码
```object-c
@define API_URL @"http://localhost:3000” 
@implementation ConfigManager 
RCT_EXPORT_MODULE(); 
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getApiUrl) 
{ 
    return API_URL; 
} 
@end
```
```java
@ReactMethod(isBlockingSynchronousMethod = true)
public String getApiUrl() {
    return "http://localhost:3000";
}
```
```javascript
import { NativeModules } from 'react-native’; 
const apiUrl = NativeModules.ConfigManager.getApiUrl();
```

# 注意
同步方法不支持 js remote 方式调试，会报错。
![rn-sync-function](http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/rn-sync-function-error.png)

解决方法，调试同步函数用alert方式，并关闭 js debug remote

# 参考 
* https://til.hashrocket.com/posts/hxfbncpqdn-export-a-synchronous-method-in-react-native-module
* https://blog.csdn.net/u011068702/article/details/82793854