---
title: "Curl 使用手册"
date: 2018-01-01T16:01:23+08:00
lastmod: 2018-01-02T16:01:23+08:00
draft: false                      
tags: ["curl"]
categories: ["工具合集"]              
author: "cathay"
---

[curl ](https://curl.haxx.se/) 是一种命令行工具，作用是发出网络请求，然后得到和提取数据，显示在"标准输出"（stdout）上面。

它支持多种协议，下面举例讲解如何将它用于网站开发。

| 参数                      | 目的                                                         | 示例                                                         |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 无                        | 查看网页源码                                                 | `curl www.sina.com`                                          |
| `-o`                      | `-o`参数将服务器的回应保存成文件，等同于`wget`命令。         | `curl -o [文件名] www.sina.com`                              |
| `-O`                      | `-O`参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。 | `curl -O https://www.example.com/foo/bar.html`<br />上面命令将服务器回应保存成文件，文件名为`bar.html`。 |
| `-L`                      | `-L`参数会让 HTTP 请求跟随服务器的重定向。curl 默认不跟随重定向。 | `curl -L www.sina.com`                                       |
| `-i`                      | `-i`参数可以显示 http response 的头信息，连同网页代码一起。  | `curl -i www.sina.com`                                       |
| `-I`                      | `-I`参数则是只显示 http response 的头信息。`--head`参数等同于`-I`。 | `curl -I www.sina.com`                                       |
| `-v`                      | `-v`参数输出通信的整个过程，用于调试。                       | `curl -v www.sina.com`                                       |
| `--trace`                 | 显示一次 http 更详细的通信的整个过程，包括端口连接和 http request 头信息。 | `curl --trace output.txt www.sina.com`<br />`curl --trace-ascii output.txt www.sina.com` |
| 无                        | GET 方法相对简单，只要把数据附在网址后面就行。               | `curl example.com/form.cgi?data=xxx`                         |
| `-X POST`                 | POST 方法必须把数据和网址分开，curl 就要用到 --data 参数。   | `curl -X POST --data "data=xxx" example.com/form.cgi`        |
| `-X POST--data-urlencode` | curl 为你编码参数`--data-urlencode`                          | `curl -X POST--data-urlencode "date=April 1" example.com/form.cgi` |
| `-X PUT/DELTE/...`        | curl 默认的 HTTP 动词是 GET，使用`-X`参数可以支持其他动词。  | `curl -X POST www.example.com`                               |
| `--form`                  | 文件上传。                                                   | `curl --form upload=@localfilename --form press=OK [URL]`    |
| `--referer`               | http request 头信息中提供一个referer字段。                   | `curl --referer http://www.example.com http://www.example.com` |
| `--user-agent`            | http request 头信息中提供一个User Agent字段。                | ` curl --user-agent "[User Agent]" [URL]`                    |
| `--cookie`                | curl 发送 cookie。                                           | `curl --cookie "name=xxx" www.example.com`                   |
| `-c cookie-file`          | 保存服务器返回的 cookie 到文件。                             | `curl -c cookies http://example.com`                         |
| `-b cookie-file`          | 可以使用这个文件作为 cookie 信息，进行后续的请求。           | `curl -b cookies http://example.com`                         |
| `--header`                | 在http request之中，自行增加一个头信息。                     | `curl --header "Content-Type:application/json" http://example.com` |
| `--user`                  | HTTP认证，这时curl需要用到`--user`参数。                     | `curl --user name:password example.com`                      |
| `-A`                      | http request 头信息中提供一个User Agent字段。                | `curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://google.com` |
| `-b`                      | curl 发送 cookie。                                           | `curl -b 'foo1=bar;foo2=bar2' https://google.com`            |
| `-c`                      | `-c`参数将服务器设置的 Cookie 写入一个文件。                 | `curl -c cookies.txt https://www.google.com`                 |
| `-d`                      | `-d`参数用于发送 POST 请求的数据体。<br />使用`-d`参数以后，HTTP 请求会自动加上标头`Content-Type : application/x-www-form-urlencoded`。<br />并且会自动将请求转为 POST 方法，因此可以省略`-X POST`。<br />`-d`参数可以读取本地文本文件的数据，向服务器发送。 | `$ curl -d'login=emma＆password=123'-X POST https://google.com/login ` <br />`curl -d 'login=emma' -d 'password=123' -X POST  https://google.com/login`<br />`curl -d '@data.txt' https://google.com/login` |
| --data-urlencode          | `--data-urlencode`参数等同于`-d`，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码。 | `curl --data-urlencode 'comment=hello world' https://google.com/login` |
| -e                        | `-e`参数用来设置 HTTP 的标头`Referer`，表示请求的来源。      | `curl -e 'https://google.com?q=example' https://www.example.com` |
| -F                        | `-F`参数用来向服务器上传二进制文件。`-F`参数可以指定 MIME 类型。 | `curl -F 'file=@photo.png' https://google.com/profile`<br />HTTP 请求加上标头`Content-Type: multipart/form-data`，然后将文件`photo.png`作为`file`字段上传。<br />`-F`参数可以指定 MIME 类型。<br />`curl -F 'file=@photo.png;type=image/png' https://google.com/profile` <br />上面命令指定 MIME 类型为`image/png`，否则 curl 会把 MIME 类型设为`application/octet-stream`。<br />`-F`参数也可以指定文件名。<br />`curl -F 'file=@photo.png;filename=me.png' https://google.com/profile`<br />上面命令中，原始文件名为`photo.png`，但是服务器接收到的文件名为`me.png`。 |
| -G                        | `-G`参数用来构造 URL 的查询字符串。                          | `curl -G -d 'q=kitties' -d 'count=20' https://google.com/search`<br />上面命令会发出一个 GET 请求，实际请求的 URL 为`https://google.com/search?q=kitties&count=20`。如果省略`--G`，会发出一个 POST 请求。<br />如果数据需要 URL 编码，可以结合`--data--urlencode`参数。<br />`curl -G --data-urlencode 'comment=hello world' https://www.example.com` |
| -H                        | `-H`参数添加 HTTP 请求的标头。                               | `curl -H 'Accept-Language: en-US' -H 'Secret-Message: xyzzy' https://google.com`<br />`curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login` |
| -K                        | `-k`参数指定跳过 SSL 检测。                                  | `curl -k https://www.example.com`<br />上面命令不会检查服务器的 SSL 证书是否正确。 |
| --limit-rate              | `--limit-rate`用来限制 HTTP 请求和回应的带宽，模拟慢网速的环境。 | `curl --limit-rate 200k https://google.com`<br />上面命令将带宽限制在每秒 200K 字节。 |
| -s                        | `-s`参数将不输出错误和进度信息。                             | `curl -s https://www.example.com`<br />上面命令一旦发生错误，不会显示错误信息。不发生错误的话，会正常显示运行结果。<br />如果想让 curl 不产生任何输出，可以使用下面的命令。<br />`curl -s -o /dev/null https://google.com` |
| -S                        | `-S`参数指定只输出错误信息，通常与`-o`一起使用。             | `curl -S -o /dev/null https://google.com`<br />上面命令没有任何输出，除非发生错误。 |
| -u                        | `-u`参数用来设置服务器认证的用户名和密码。                   | `curl -u 'bob:12345' https://google.com/login`<br />上面命令设置用户名为`bob`，密码为`12345`，然后将其转为 HTTP 标头`Authorization: Basic Ym9iOjEyMzQ1`。<br />curl 能够识别 URL 里面的用户名和密码。<br />`curl https://bob:12345@google.com/login`<br />上面命令能够识别 URL 里面的用户名和密码，将其转为上个例子里面的 HTTP 标头。<br />`curl -u 'bob' https://google.com/login`<br />上面命令只设置了用户名，执行后，curl 会提示用户输入密码。 |
| -x                        | `-x`参数指定 HTTP 请求的代理。如果没有指定代理协议，默认为 HTTP。 | `curl -x socks5://james:cats@myproxy.com:8080 https://www.example.com` |
|                           |                                                              |                                                              |
|                           |                                                              |                                                              |
|                           |                                                              |                                                              |
