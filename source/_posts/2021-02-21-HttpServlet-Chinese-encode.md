---
title: Servlet文件下载中文文件名乱码的解决
date: 2021-02-21 10:52:07
comments: false
tags: 
    - web
    - java
categories: 
    - [java]
---

{% blockquote %}
Http header中只支持ASCII，所以在下载中文名称文件时，在UrlEncode时需正确处理字符集，不然极易出现中文乱码的问题。
{% endblockquote %}

在使用HttpServletResponse来实现文件下载时，可以设置response header `Content-Disposition`来指定下载的文件名。
如果文件名包含中文字符，需参考下方代码来避免出现中文乱码。

{% codeblock lang:java %}
fileName = URLEncoder.encode(fileName, "UTF-8").replaceAll("\\+", "%20");
response.setContentType("application/octet-stream");
response.setCharacterEncoding("UTF-8");
StringBuilder sb = new StringBuilder("attachment;");
sb.append(" filename=\"").append(fileName).append("\";");
sb.append(" filename*=utf-8''").append(fileName);
response.setHeader("Content-Disposition", sb.toString());
{% endcodeblock %}

值得关注的是这一行代码: `sb.append(" filename*=utf-8''").append(fileName);`。
我们在通过`filename=xxxx`指定url encode后的文件名后，还需要通过`filename*=charset`告诉浏览器使用何种字符集来解码文件名。

这种解决方案在各种现代浏览器均通过验证，`Chrome`、`Firefox`、`Microsoft Edge`、`PostMan`均可完美下载中文名称文件。