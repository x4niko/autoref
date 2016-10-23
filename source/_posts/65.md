---
title: android.os.NetworkOnMainThreadException
tags:
  - android error
  - BUG
categories:
  - android
  - android error
date: 2016-02-25 10:12:05
---

错误日志：
``` bash
android.os.NetworkOnMainThreadException
```
错误原因：在主线程中操作网络请求。
解决办法：将网络请求操作单独放到一个线程中。
比如：
``` bash
new Thread(){
      @Override
      public void run()
      {
        // TODO: 网络操作
      }  
}.start();  
```
或者：
``` bash
class DoSomeTask extends AsyncTask<String, Void, String> {

    protected RSSFeed doInBackground(String... urls) {
        // TODO: 网络操作
    }

    protected void onPostExecute(String result) {
        // TODO: do something
    }
}
```
