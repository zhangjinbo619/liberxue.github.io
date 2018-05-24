---
layout: blog
tech: true
title: "react native 0.44.3 ios webview 102 error"
background-image: "http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/ios-webview-102.png"
date: "2017-11-23"
category: code #book,game,note,chat,code,image,web,link,design,lock
tags:
- rn
- react native
- webview
---

![102 error](http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/code-sum.jpg)

# 目的
  app项目首页banner功能，点击进入活动页面。由于web微站早已上线，为了不重复开发
  （其实就是偷懒，为啥要重复开发一个一模一样的页面，而且用rn构建活动页，那么多不同的模板，搞死人咯🤣）。
# 思路
  1. 最终采取采取app内嵌微站webview页面。
  2. 点击banner跳转至微站web页面。
  3. 活动页点击商品跳转至原生商品详情页（其实后来考虑抽象的WebViewFilter.js业务抽象类，原则是有原生就跳原生）。

# 遇到的坑
 点击banner进入拦截器了并且也跳转了对应的原生页面，可是从详情页返回后，尽然。。。😌，什么鬼？？
 ![102 error](http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/ios-webview-102.png?x-oss-process=image/resize,w_400)

# 分析

## 分析ios报错

  1. 参考 [Domain=WebKitErrorDomain Code=102](https://blog.csdn.net/wanglei0918/article/details/77237263) 内容，说是因为url不完整，在网站前拼接一个@”http://”可以解决。排查了下，不是上述问题，url是完整的。

  2. 看ios日志
  ``` objectivec
    void SendDelegateMessage(NSInvocation *): delegate (webView:decidePolicyForNavigationAction:request:frame:decisionListener:) failed to return after waiting 10 seconds. main run loop mode: kCFRunLoopDefaultMode
  ```
  查了下说好像是因为多次发送请求导致的，就是多次同时创建 iframe被ios拦截导致卡住了。我看了下页面没有iframe，并且没有多次请求的情况，我就是点了一个商品的连接啊啊啊，ORZ。。。。

## 分析web页面代码
  1. 查看具体活动页面浏览器中的html代码,其跳转的url为“/mobile/product/content/201706/147149.html”
  ``` html
        <li>
          <a href="/mobile/product/content/201706/147149.html" class="ui-link">
              <div>
                  <img src="http://image.com/n1/jfs/t2479/126/1824111579/264665/d911e030/56de95a0Nbe0fcefa.jpg" alt="">
              </div>    
              <div class="txt-con">
                  <p class="txt-con-name">水星家纺出品 百丽丝家纺 全棉斜纹印花四件套 浮岗翠 双人1.5米床</p>
                  <div class="yPrice">
                      <span class="pText">抢购价:</span>
                      <span class="pColor">暂无报价</span>
                  </div>
              </div>
          </a>
      </li>
  ```
  2. 可是点击后跳转页面却是，http://www.website.com/mobile/product/content.jhtml?brandId=3524&productCategoryId=1626&productId=147149，什么鬼？

  3. 去看服务端代码后发现服务端有拦截器，用于判断客户端请求是移动端，还是pc端，最终redirect到对应请求页面。所以是不是因为重定向导致的问题呢？
     别的页面没重定向页面都正常，只有详情页有问题，所以判断是因为重定向导致了102.

# 解决方法

  根据下面参考的连接，我采用了如下方法解决,找到RN webview的源码路径为：项目名称/node_modules/react-native/React/Views/RCTWebView.m 

  ``` objectivec
      - (void)webView:(__unused UIWebView *)webView didFailLoadWithError:(NSError *)error
      {
        // Ignore NSURLErrorDomain error -999.
        if (error.code == NSURLErrorCancelled) return;
        
        // Ignore "Fame Load Interrupted" errors. Seen after app store links.
        if (error.code == 102 && [error.domain isEqual:@"WebKitErrorDomain"]) return;

        if (_onLoadingError) {
          if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorCancelled) {
            // NSURLErrorCancelled is reported when a page has a redirect OR if you load
            // a new URL in the WebView before the previous one came back. We can just
            // ignore these since they aren't real errors.
            // http://stackoverflow.com/questions/1024748/how-do-i-fix-nsurlerrordomain-error-999-in-iphone-3-0-os
            return;
          }

          NSMutableDictionary<NSString *, id> *event = [self baseEvent];
          [event addEntriesFromDictionary:@{
            @"domain": error.domain,
            @"code": @(error.code),
            @"description": error.localizedDescription,
          }];
          _onLoadingError(event);
        }
      }

  ```


# 参考
webview error code:102 [Domain=WebKitErrorDomain Code=102](https://stackoverflow.com/questions/4299403/how-to-handle-app-urls-in-a-uiwebview?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)
