---
title: Android的WebView组件忽略SSL证书验证错误漏洞
tags:
  - android
  - 移动安全
categories:
  - android
  - android安全
date: 2016-08-27 23:42:39
---

漏洞描述：
Android WebView组件加载网页发生证书认证错误时，会调用WebViewClient类的onReceivedSslError方法，如果该方法实现调用了handler.proceed()来忽略该证书错误，则会受到中间人攻击的威胁，可能导致隐私泄露。

实现onReceivedSslError的处理，漏洞代码样例：
``` bash
  ...
  mWebView.loadUrl("http://autoref.cn");
  mWebView.setWebViewClient(new WebViewClient() {
      @Override
      public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
          handler.proceed(); // 忽略SSL认证
      }
  });
  ...
```
如上代码在onReceivedSslError处理时没有进行cancel，还是使用了proceed()，忽略掉了发生的SSL异常。

解决方案：
当发生证书认证错误时，采用默认的处理方法handler.cancel()，停止加载问题页面。
