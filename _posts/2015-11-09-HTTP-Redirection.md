---
layout: post
title: Http Redirection
author: 聪记
header-img: img/post_header_universe.jpg
tags:
    - HTTP
---

## HTTP Redirection

看OkHttp源码的时候注意到下面代码[HttpEngine#followUpRequest](https://github.com/square/okhttp/blob/master/okhttp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fsquareup%2Fokhttp%2Finternal%2Fhttp%2FHttpEngine.java)：

```
public Request followUpRequest() throws IOException {

    ···
    int responseCode = userResponse.code();

    switch (responseCode) {

      ···
      case HTTP_PERM_REDIRECT:
      case HTTP_TEMP_REDIRECT:
        // "If the 307 or 308 status code is received in response to a request other than GET
        // or HEAD, the user agent MUST NOT automatically redirect the request"
        if (!userRequest.method().equals("GET") && !userRequest.method().equals("HEAD")) {
            return null;
        }
        // fall-through
      case HTTP_MULT_CHOICE:
      case HTTP_MOVED_PERM:
      case HTTP_MOVED_TEMP:
      case HTTP_SEE_OTHER:

        ···
        String location = userResponse.header("Location");
        if (location == null) return null;
        HttpUrl url = userRequest.httpUrl().resolve(location);
        ···
        // Redirects don't include a request body.
        Request.Builder requestBuilder = userRequest.newBuilder();
        if (HttpMethod.permitsRequestBody(userRequest.method())) {
          requestBuilder.method("GET", null);
          requestBuilder.removeHeader("Transfer-Encoding");
          requestBuilder.removeHeader("Content-Length");
          requestBuilder.removeHeader("Content-Type");
        }

        // When redirecting across hosts, drop all authentication headers. This
        // is potentially annoying to the application layer since they have no
        // way to retain them.
        if (!sameConnection(url)) {
          requestBuilder.removeHeader("Authorization");
        }

        return requestBuilder.url(url).build();
        ···
    }
  }
```

这是OkHttp默认的HTTP重定向处理方法，该方法生成请求重定向地址的`Request`。

* 如果status code是`307`或者`308`，则只允许**GET**和**HEAD**请求。
* 如果status code是`300`, `301`, `302`, `303`, 则会把请求强行转换为**GET**方法，并移除request body,。此外如果是跨站请求还会移除验证信息。

### HTTP Redirection

Redirection([rfc7231#section-6.4](https://tools.ietf.org/html/rfc7231#section-6.4)):

> The 3xx (Redirection) class of status code indicates that further
> action needs to be taken by the user agent in order to fulfill the
> request.

跟重定向相关的Status Code主要有以下几个：

301 | 302 | 303 | 304 | 307 | 308 |
---- | ---- | ---- | ---- | ---- | ---- |
Moved Permanently | Found | See Other |  Not Modified | Temporary Redirect | Permanent Redirect ([rfc7538](https://tools.ietf.org/html/rfc7238)) |

* 302在HTTP1.0中定义为*Moved Temporarily*, 在1.1中新增了303， 307, 308作为补充和区分
* 301/302和307/308的不同在于301/302允许客户端将原请求从`POST`改为`GET`，307/308则不允许做此改变
* 大部分的浏览器并没有按照标准处理好301/302状态，有些将302与303一视同仁，即把`POST`改为`GET`自动跳转，有些不改变`POST`方法直接跳转，但是移除了request body
* 303状态在Web开发中常用于[Post-Redirect-Get](https://en.wikipedia.org/wiki/Post/Redirect/Get)模式来避免客户端表单重复提交
* 只有safe method可以自动跳转，[这里](https://tools.ietf.org/html/rfc7231#section-4.2.1)是完整定义，简单地说就是`read-only`的方法可以不经过用户确认而自动跳转，*GET*, *HEAD*, *OPTIONS*, *TRACE*。
* 出于安全的考虑，非safe method的重定向后必须(**MUST**)经过用户确认才可跳转
* Redirection可以被用来追踪用户的浏览路径，[Logging outgoing links](https://en.wikipedia.org/wiki/URL_redirection#Logging_outgoing_links)
