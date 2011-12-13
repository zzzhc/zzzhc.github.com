---
layout: post
title: "有道字典 chrome extension"
date: 2011-12-12 11:17
comments: true
categories: misc
---

有道字典的chrome extension会把鼠标下的词log到console里，debug的时候让人烦, 看了下code, 直接用的console.log, 发布的时候也没注释掉. 跑到~/Library/Application Support/Google/Chrome/Default/Extensions/nbndkplefmmhmcmfjanjaakhhkiegogd/1.0_0下把content.js,background.html里的console.log都注释掉，安静了。

大概看了下extension code, 发现两个有意思的地方：

* 打包的时候连.svn目录都没放过

``` bash
$ svn info
Path: .
URL: https://dev.corp.youdao.com/svn/outfox/products/desktop/incubator/mac/GetWordExtension/Chrome/extension
Repository Root: https://dev.corp.youdao.com/svn/outfox
Repository UUID: 36a6777f-fe3c-0410-890b-904d6044f29d
Revision: 285097
Node Kind: directory
Schedule: normal
Last Changed Author: huangdx
Last Changed Rev: 277738
Last Changed Date: 2011-09-13 14:00:17 +0800 (二, 13  9 2011)
```

* 取词的时候调用的是本机有道字典app提供的http接口

``` javascript
//in background.html
     function SendResult(word, pos, type) {
         var s = new XMLHttpRequest;
         s.open("GET", "http://localhost:32445/getword?word=" + word + "&pos=" + pos + "&type=" + type, true);
         //console.log('sending...')
         s.send()
     };
```

用curl试了下，可以发送请求，但响应为空

``` bash
$ curl -vv 'http://localhost:32445/getword?word=for%20suppliers&pos=8&type=0'
* About to connect() to localhost port 32445 (#0)
*   Trying ::1... Connection refused
*   Trying 127.0.0.1... connected
* Connected to localhost (127.0.0.1) port 32445 (#0)
> GET /getword?word=for%20suppliers&pos=8&type=0 HTTP/1.1
> User-Agent: curl/7.21.4 (universal-apple-darwin11.0) libcurl/7.21.4 OpenSSL/0.9.8r zlib/1.2.5
> Host: localhost:32445
> Accept: */*
> 
* Empty reply from server
* Connection #0 to host localhost left intact
curl: (52) Empty reply from server
* Closing connection #0
```

word, pos, type这三个参数只要少一个有道字典就会crash

这次http request只是一个trigger, 有道字典会向dict.youdao.com发一个request:

``` bash
$ curl -vv 'http://dict.youdao.com/fsearch?q=for%20suppliers&pos=8&keyfrom=mac.scrtrans.0&id=E33EC7736AFDCABB184051CD3757CA73&vendor=cidian.youdao.com&client=macdict'
* About to connect() to dict.youdao.com port 80 (#0)
*   Trying 61.135.218.32... connected
* Connected to dict.youdao.com (61.135.218.32) port 80 (#0)
> GET /fsearch?q=for%20suppliers&pos=8&keyfrom=mac.scrtrans.0&id=E33EC7736AFDCABB184051CD3757CA73&vendor=cidian.youdao.com&client=macdict HTTP/1.1
> User-Agent: curl/7.21.4 (universal-apple-darwin11.0) libcurl/7.21.4 OpenSSL/0.9.8r zlib/1.2.5
> Host: dict.youdao.com
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx
< Date: Tue, 13 Dec 2011 07:10:55 GMT
< Content-Type: text/xml; charset=utf-8
< Connection: keep-alive
< Cache-Control: private
< Content-Language: en-US
< Set-Cookie: OUTFOX_SEARCH_USER_ID=-447518408@123.117.56.163; domain=.youdao.com; expires=Thu, 05-Dec-2041 07:10:55 GMT
< Set-Cookie: JSESSIONID=abcdjNBcCWPPK4igOq2qt; domain=youdao.com; path=/
< Vary: Accept-Encoding
< Content-Length: 1477
< 
<?xml version="1.0" encoding="UTF-8"?>

<yodaodict>
  <return-phrase><![CDATA[suppliers]]></return-phrase>
  
                            <phonetic-symbol>sə'plaiəz</phonetic-symbol>
                  <dictcn-speach>suppliers</dictcn-speach>
                          <custom-translation>
        <type>ec</type>
                  <translation><content><![CDATA[n. 供应商（supplier的复数）]]></content></translation>
                </custom-translation>
          
              <yodao-web-dict>
                    <web-translation>
                <key><![CDATA[Suppliers]]></key>
                          <trans><value><![CDATA[供应商]]></value></trans>
                  <trans><value><![CDATA[供货商]]></value></trans>
                  <trans><value><![CDATA[数据库]]></value></trans>
                </web-translation>
              <web-translation>
                <key><![CDATA[Overview Suppliers]]></key>
                          <trans><value><![CDATA[概览]]></value></trans>
                  <trans><value><![CDATA[供应商]]></value></trans>
                </web-translation>
              <web-translation>
                <key><![CDATA[select suppliers]]></key>
                          <trans><value><![CDATA[挑选供应商]]></value></trans>
                </web-translation>
            </yodao-web-dict>
    
              	 	       	 <recommend><![CDATA[supplier]]></recommend>
                    <sexp>0</sexp>
</yodaodict>
* Connection #0 to host dict.youdao.com left intact
* Closing connection #0
```

拿到结果后由有道字典显示一个提示窗口.

这种实现方式倒也算精巧, 其他app要用这个http接口也比较方便，只是32445端口连个http 200都不返回粗暴了点.
