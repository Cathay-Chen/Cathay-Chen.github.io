---
title: "Curl 使用手册"
date: 2022-07-31T20:01:23+08:00
lastmod: 2022-08-01T10:01:23+08:00
draft: false                      
tags: ["curl"]
categories: ["工具合集"]              
author: "cathay"
---

[curl ](https://curl.haxx.se/) 是一种命令行工具，作用是发出网络请求，然后得到和提取数据，显示在"标准输出"（stdout）上面。

它支持多种协议，下面举例讲解如何将它用于网站开发。(摘自阮一峰老师博客)

## 表格展示参数示例

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
| `--data-urlencode`        | `--data-urlencode`参数等同于`-d`，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码。 | `curl --data-urlencode 'comment=hello world' https://google.com/login` |
| `-e`                      | `-e`参数用来设置 HTTP 的标头`Referer`，表示请求的来源。      | `curl -e 'https://google.com?q=example' https://www.example.com` |
| `-F`                      | `-F`参数用来向服务器上传二进制文件。`-F`参数可以指定 MIME 类型。 | `curl -F 'file=@photo.png' https://google.com/profile`<br />HTTP 请求加上标头`Content-Type: multipart/form-data`，然后将文件`photo.png`作为`file`字段上传。<br />`-F`参数可以指定 MIME 类型。<br />`curl -F 'file=@photo.png;type=image/png' https://google.com/profile` <br />上面命令指定 MIME 类型为`image/png`，否则 curl 会把 MIME 类型设为`application/octet-stream`。<br />`-F`参数也可以指定文件名。<br />`curl -F 'file=@photo.png;filename=me.png' https://google.com/profile`<br />上面命令中，原始文件名为`photo.png`，但是服务器接收到的文件名为`me.png`。 |
| `-G`                      | `-G`参数用来构造 URL 的查询字符串。                          | `curl -G -d 'q=kitties' -d 'count=20' https://google.com/search`<br />上面命令会发出一个 GET 请求，实际请求的 URL 为`https://google.com/search?q=kitties&count=20`。如果省略`--G`，会发出一个 POST 请求。<br />如果数据需要 URL 编码，可以结合`--data--urlencode`参数。<br />`curl -G --data-urlencode 'comment=hello world' https://www.example.com` |
| `-H`                      | `-H`参数添加 HTTP 请求的标头。                               | `curl -H 'Accept-Language: en-US' -H 'Secret-Message: xyzzy' https://google.com`<br />`curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login` |
| `-K`                      | `-k`参数指定跳过 SSL 检测。                                  | `curl -k https://www.example.com`<br />上面命令不会检查服务器的 SSL 证书是否正确。 |
| `--limit-rate`            | `--limit-rate`用来限制 HTTP 请求和回应的带宽，模拟慢网速的环境。 | `curl --limit-rate 200k https://google.com`<br />上面命令将带宽限制在每秒 200K 字节。 |
| `-s`                      | `-s`参数将不输出错误和进度信息。                             | `curl -s https://www.example.com`<br />上面命令一旦发生错误，不会显示错误信息。不发生错误的话，会正常显示运行结果。<br />如果想让 curl 不产生任何输出，可以使用下面的命令。<br />`curl -s -o /dev/null https://google.com` |
| `-S`                      | `-S`参数指定只输出错误信息，通常与`-o`一起使用。             | `curl -S -o /dev/null https://google.com`<br />上面命令没有任何输出，除非发生错误。 |
| `-u`                      | `-u`参数用来设置服务器认证的用户名和密码。                   | `curl -u 'bob:12345' https://google.com/login`<br />上面命令设置用户名为`bob`，密码为`12345`，然后将其转为 HTTP 标头`Authorization: Basic Ym9iOjEyMzQ1`。<br />curl 能够识别 URL 里面的用户名和密码。<br />`curl https://bob:12345@google.com/login`<br />上面命令能够识别 URL 里面的用户名和密码，将其转为上个例子里面的 HTTP 标头。<br />`curl -u 'bob' https://google.com/login`<br />上面命令只设置了用户名，执行后，curl 会提示用户输入密码。 |
| `-x`                      | `-x`参数指定 HTTP 请求的代理。如果没有指定代理协议，默认为 HTTP。 | `curl -x socks5://james:cats@myproxy.com:8080 https://www.example.com` |
| `-R`                      | `--remote-time` 在本地生成文件时，保留远程文件时间           |                                                              |
| `--resolve`               | `--resolve HOST:PORT:ADDRESS` 将 HOST:PORT 强制解析到 ADDRESS。 |                                                              |
| `-T`                      | `-T` "指定请求体文件"                                        |                                                              |

## curl --help all 输出

```shell
Usage: curl [options...] <url>
     --abstract-unix-socket <path>  Connect via abstract Unix domain socket
     --alt-svc <file name>  Enable alt-svc with this cache file
     --anyauth       Pick any authentication method
 -a, --append        Append to target file when uploading
     --aws-sigv4 <provider1[:provider2[:region[:service]]]>  Use AWS V4 signature authentication
     --basic         Use HTTP Basic Authentication
     --cacert <file>  CA certificate to verify peer against
     --capath <dir>  CA directory to verify peer against
 -E, --cert <certificate[:password]>  Client certificate file and password
     --cert-status   Verify the status of the server cert via OCSP-staple
     --cert-type <type>  Certificate type (DER/PEM/ENG)
     --ciphers <list of ciphers>  SSL ciphers to use
     --compressed    Request compressed response
     --compressed-ssh  Enable SSH compression
 -K, --config <file>  Read config from a file
     --connect-timeout <seconds>  Maximum time allowed for connection
     --connect-to <HOST1:PORT1:HOST2:PORT2>  Connect to host
 -C, --continue-at <offset>  Resumed transfer offset
 -b, --cookie <data|filename>  Send cookies from string/file
 -c, --cookie-jar <filename>  Write cookies to <filename> after operation
     --create-dirs   Create necessary local directory hierarchy
     --create-file-mode <mode>  File mode (octal) for created files
     --crlf          Convert LF to CRLF in upload
     --crlfile <file>  Get a CRL list in PEM format from the given file
     --curves <algorithm list>  (EC) TLS key exchange algorithm(s) to request
 -d, --data <data>   HTTP POST data
     --data-ascii <data>  HTTP POST ASCII data
     --data-binary <data>  HTTP POST binary data
     --data-raw <data>  HTTP POST data, '@' allowed
     --data-urlencode <data>  HTTP POST data url encoded
     --delegation <LEVEL>  GSS-API delegation permission
     --digest        Use HTTP Digest Authentication
 -q, --disable       Disable .curlrc
     --disable-eprt  Inhibit using EPRT or LPRT
     --disable-epsv  Inhibit using EPSV
     --disallow-username-in-url  Disallow username in url
     --dns-interface <interface>  Interface to use for DNS requests
     --dns-ipv4-addr <address>  IPv4 address to use for DNS requests
     --dns-ipv6-addr <address>  IPv6 address to use for DNS requests
     --dns-servers <addresses>  DNS server addrs to use
     --doh-cert-status  Verify the status of the DOH server cert via OCSP-staple
     --doh-insecure  Allow insecure DOH server connections
     --doh-url <URL>  Resolve host names over DOH
 -D, --dump-header <filename>  Write the received headers to <filename>
     --egd-file <file>  EGD socket path for random data
     --engine <name>  Crypto engine to use
     --etag-compare <file>  Pass an ETag from a file as a custom header
     --etag-save <file>  Parse ETag from a request and save it to a file
     --expect100-timeout <seconds>  How long to wait for 100-continue
 -f, --fail          Fail silently (no output at all) on HTTP errors
     --fail-early    Fail on first transfer error, do not continue
     --fail-with-body  Fail on HTTP errors but save the body
     --false-start   Enable TLS False Start
 -F, --form <name=content>  Specify multipart MIME data
     --form-string <name=string>  Specify multipart MIME data
     --ftp-account <data>  Account data string
     --ftp-alternative-to-user <command>  String to replace USER [name]
     --ftp-create-dirs  Create the remote dirs if not present
     --ftp-method <method>  Control CWD usage
     --ftp-pasv      Use PASV/EPSV instead of PORT
 -P, --ftp-port <address>  Use PORT instead of PASV
     --ftp-pret      Send PRET before PASV
     --ftp-skip-pasv-ip  Skip the IP address for PASV
     --ftp-ssl-ccc   Send CCC after authenticating
     --ftp-ssl-ccc-mode <active/passive>  Set CCC mode
     --ftp-ssl-control  Require SSL/TLS for FTP login, clear for transfer
 -G, --get           Put the post data in the URL and use GET
 -g, --globoff       Disable URL sequences and ranges using {} and []
     --happy-eyeballs-timeout-ms <milliseconds>  Time for IPv6 before trying IPv4
     --haproxy-protocol  Send HAProxy PROXY protocol v1 header
 -I, --head          Show document info only
 -H, --header <header/@file>  Pass custom header(s) to server
 -h, --help <category>  Get help for commands
     --hostpubmd5 <md5>  Acceptable MD5 hash of the host public key
     --hsts <file name>  Enable HSTS with this cache file
     --http0.9       Allow HTTP 0.9 responses
 -0, --http1.0       Use HTTP 1.0
     --http1.1       Use HTTP 1.1
     --http2         Use HTTP 2
     --http2-prior-knowledge  Use HTTP 2 without HTTP/1.1 Upgrade
     --http3         Use HTTP v3
     --ignore-content-length  Ignore the size of the remote resource
 -i, --include       Include protocol response headers in the output
 -k, --insecure      Allow insecure server connections when using SSL
     --interface <name>  Use network INTERFACE (or address)
 -4, --ipv4          Resolve names to IPv4 addresses
 -6, --ipv6          Resolve names to IPv6 addresses
 -j, --junk-session-cookies  Ignore session cookies read from file
     --keepalive-time <seconds>  Interval time for keepalive probes
     --key <key>     Private key file name
     --key-type <type>  Private key file type (DER/PEM/ENG)
     --krb <level>   Enable Kerberos with security <level>
     --libcurl <file>  Dump libcurl equivalent code of this command line
     --limit-rate <speed>  Limit transfer speed to RATE
 -l, --list-only     List only mode
     --local-port <num/range>  Force use of RANGE for local port numbers
 -L, --location      Follow redirects
     --location-trusted  Like --location, and send auth to other hosts
     --login-options <options>  Server login options
     --mail-auth <address>  Originator address of the original email
     --mail-from <address>  Mail from this address
     --mail-rcpt <address>  Mail to this address
     --mail-rcpt-allowfails  Allow RCPT TO command to fail for some recipients
 -M, --manual        Display the full manual
     --max-filesize <bytes>  Maximum file size to download
     --max-redirs <num>  Maximum number of redirects allowed
 -m, --max-time <seconds>  Maximum time allowed for the transfer
     --metalink      Process given URLs as metalink XML file
     --negotiate     Use HTTP Negotiate (SPNEGO) authentication
 -n, --netrc         Must read .netrc for user name and password
     --netrc-file <filename>  Specify FILE for netrc
     --netrc-optional  Use either .netrc or URL
 -:, --next          Make next URL use its separate set of options
     --no-alpn       Disable the ALPN TLS extension
 -N, --no-buffer     Disable buffering of the output stream
     --no-keepalive  Disable TCP keepalive on the connection
     --no-npn        Disable the NPN TLS extension
     --no-progress-meter  Do not show the progress meter
     --no-sessionid  Disable SSL session-ID reusing
     --noproxy <no-proxy-list>  List of hosts which do not use proxy
     --ntlm          Use HTTP NTLM authentication
     --ntlm-wb       Use HTTP NTLM authentication with winbind
     --oauth2-bearer <token>  OAuth 2 Bearer Token
 -o, --output <file>  Write to file instead of stdout
     --output-dir <dir>  Directory to save files in
 -Z, --parallel      Perform transfers in parallel
     --parallel-immediate  Do not wait for multiplexing (with --parallel)
     --parallel-max  Maximum concurrency for parallel transfers
     --pass <phrase>  Pass phrase for the private key
     --path-as-is    Do not squash .. sequences in URL path
     --pinnedpubkey <hashes>  FILE/HASHES Public key to verify peer against
     --post301       Do not switch to GET after following a 301
     --post302       Do not switch to GET after following a 302
     --post303       Do not switch to GET after following a 303
     --preproxy [protocol://]host[:port]  Use this proxy first
 -#, --progress-bar  Display transfer progress as a bar
     --proto <protocols>  Enable/disable PROTOCOLS
     --proto-default <protocol>  Use PROTOCOL for any URL missing a scheme
     --proto-redir <protocols>  Enable/disable PROTOCOLS on redirect
 -x, --proxy [protocol://]host[:port]  Use this proxy
     --proxy-anyauth  Pick any proxy authentication method
     --proxy-basic   Use Basic authentication on the proxy
     --proxy-cacert <file>  CA certificate to verify peer against for proxy
     --proxy-capath <dir>  CA directory to verify peer against for proxy
     --proxy-cert <cert[:passwd]>  Set client certificate for proxy
     --proxy-cert-type <type>  Client certificate type for HTTPS proxy
     --proxy-ciphers <list>  SSL ciphers to use for proxy
     --proxy-crlfile <file>  Set a CRL list for proxy
     --proxy-digest  Use Digest authentication on the proxy
     --proxy-header <header/@file>  Pass custom header(s) to proxy
     --proxy-insecure  Do HTTPS proxy connections without verifying the proxy
     --proxy-key <key>  Private key for HTTPS proxy
     --proxy-key-type <type>  Private key file type for proxy
     --proxy-negotiate  Use HTTP Negotiate (SPNEGO) authentication on the proxy
     --proxy-ntlm    Use NTLM authentication on the proxy
     --proxy-pass <phrase>  Pass phrase for the private key for HTTPS proxy
     --proxy-pinnedpubkey <hashes>  FILE/HASHES public key to verify proxy with
     --proxy-service-name <name>  SPNEGO proxy service name
     --proxy-ssl-allow-beast  Allow security flaw for interop for HTTPS proxy
     --proxy-ssl-auto-client-cert  Use auto client certificate for proxy (Schannel)
     --proxy-tls13-ciphers <ciphersuite list>  TLS 1.3 proxy cipher suites
     --proxy-tlsauthtype <type>  TLS authentication type for HTTPS proxy
     --proxy-tlspassword <string>  TLS password for HTTPS proxy
     --proxy-tlsuser <name>  TLS username for HTTPS proxy
     --proxy-tlsv1   Use TLSv1 for HTTPS proxy
 -U, --proxy-user <user:password>  Proxy user and password
     --proxy1.0 <host[:port]>  Use HTTP/1.0 proxy on given port
 -p, --proxytunnel   Operate through an HTTP proxy tunnel (using CONNECT)
     --pubkey <key>  SSH Public key file name
 -Q, --quote         Send command(s) to server before transfer
     --random-file <file>  File for reading random data from
 -r, --range <range>  Retrieve only the bytes within RANGE
     --raw           Do HTTP "raw"; no transfer decoding
 -e, --referer <URL>  Referrer URL
 -J, --remote-header-name  Use the header-provided filename
 -O, --remote-name   Write output to a file named as the remote file
     --remote-name-all  Use the remote file name for all URLs
 -R, --remote-time   Set the remote file's time on the local output
 -X, --request <command>  Specify request command to use
     --request-target  Specify the target for this request
     --resolve <[+]host:port:addr[,addr]...>  Resolve the host+port to this address
     --retry <num>   Retry request if transient problems occur
     --retry-all-errors  Retry all errors (use with --retry)
     --retry-connrefused  Retry on connection refused (use with --retry)
     --retry-delay <seconds>  Wait time between retries
     --retry-max-time <seconds>  Retry only within this period
     --sasl-authzid <identity>  Identity for SASL PLAIN authentication
     --sasl-ir       Enable initial response in SASL authentication
     --service-name <name>  SPNEGO service name
 -S, --show-error    Show error even when -s is used
 -s, --silent        Silent mode
     --socks4 <host[:port]>  SOCKS4 proxy on given host + port
     --socks4a <host[:port]>  SOCKS4a proxy on given host + port
     --socks5 <host[:port]>  SOCKS5 proxy on given host + port
     --socks5-basic  Enable username/password auth for SOCKS5 proxies
     --socks5-gssapi  Enable GSS-API auth for SOCKS5 proxies
     --socks5-gssapi-nec  Compatibility with NEC SOCKS5 server
     --socks5-gssapi-service <name>  SOCKS5 proxy service name for GSS-API
     --socks5-hostname <host[:port]>  SOCKS5 proxy, pass host name to proxy
 -Y, --speed-limit <speed>  Stop transfers slower than this
 -y, --speed-time <seconds>  Trigger 'speed-limit' abort after this time
     --ssl           Try SSL/TLS
     --ssl-allow-beast  Allow security flaw to improve interop
     --ssl-auto-client-cert  Use auto client certificate (Schannel)
     --ssl-no-revoke  Disable cert revocation checks (Schannel)
     --ssl-reqd      Require SSL/TLS
     --ssl-revoke-best-effort  Ignore missing/offline cert CRL dist points
 -2, --sslv2         Use SSLv2
 -3, --sslv3         Use SSLv3
     --stderr <file>  Where to redirect stderr
     --styled-output  Enable styled output for HTTP headers
     --suppress-connect-headers  Suppress proxy CONNECT response headers
     --tcp-fastopen  Use TCP Fast Open
     --tcp-nodelay   Use the TCP_NODELAY option
 -t, --telnet-option <opt=val>  Set telnet option
     --tftp-blksize <value>  Set TFTP BLKSIZE option
     --tftp-no-options  Do not send any TFTP options
 -z, --time-cond <time>  Transfer based on a time condition
     --tls-max <VERSION>  Set maximum allowed TLS version
     --tls13-ciphers <ciphersuite list>  TLS 1.3 cipher suites to use
     --tlsauthtype <type>  TLS authentication type
     --tlspassword   TLS password
     --tlsuser <name>  TLS user name
 -1, --tlsv1         Use TLSv1.0 or greater
     --tlsv1.0       Use TLSv1.0 or greater
     --tlsv1.1       Use TLSv1.1 or greater
     --tlsv1.2       Use TLSv1.2 or greater
     --tlsv1.3       Use TLSv1.3 or greater
     --tr-encoding   Request compressed transfer encoding
     --trace <file>  Write a debug trace to FILE
     --trace-ascii <file>  Like --trace, but without hex output
     --trace-time    Add time stamps to trace/verbose output
     --unix-socket <path>  Connect through this Unix domain socket
 -T, --upload-file <file>  Transfer local FILE to destination
     --url <url>     URL to work with
 -B, --use-ascii     Use ASCII/text transfer
 -u, --user <user:password>  Server user and password
 -A, --user-agent <name>  Send User-Agent <name> to server
 -v, --verbose       Make the operation more talkative
 -V, --version       Show version number and quit
 -w, --write-out <format>  Use output FORMAT after completion
     --xattr         Store metadata in extended file attributes
```

## 命令行输出中文翻译

```shell
curl是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称url为下载工具。

 -a/--append 上传文件时，附加到目标文件 
 -A/--user-agent <string>  设置用户代理发送给服务器
 - anyauth   可以使用“任何”身份验证方法
 -b/--cookie <name=string/file> cookie字符串或文件读取位置
 - basic 使用HTTP基本验证
 -B/--use-ascii 使用ASCII /文本传输
 -c/--cookie-jar <file> 操作结束后把cookie写入到这个文件中
 -C/--continue-at <offset>  断点续转
 -d/--data <data>   HTTP POST方式传送数据
 --data-ascii <data>  以ascii的方式post数据
 --data-binary <data> 以二进制的方式post数据
 --negotiate     使用HTTP身份验证
 --digest        使用数字身份验证
 --disable-eprt  禁止使用EPRT或LPRT
 --disable-epsv  禁止使用EPSV
 -D/--dump-header <file> 把header信息写入到该文件中
 --egd-file <file> 为随机数据(SSL)设置EGD socket路径
 --tcp-nodelay   使用TCP_NODELAY选项
 -e/--referer 来源网址
 -E/--cert <cert[:passwd]> 客户端证书文件和密码 (SSL)
 --cert-type <type> 证书文件类型 (DER/PEM/ENG) (SSL)
 --key <key>     私钥文件名 (SSL)
 --key-type <type> 私钥文件类型 (DER/PEM/ENG) (SSL)
 --pass  <pass>  私钥密码 (SSL)
 --engine <eng>  加密引擎使用 (SSL). "--engine list" for list
 --cacert <file> CA证书 (SSL)
 --capath <directory> CA目录 (made using c_rehash) to verify peer against (SSL)
 --ciphers <list>  SSL密码
 --compressed    要求返回是压缩的形势 (using deflate or gzip)
 --connect-timeout <seconds> 设置最大请求时间
 --create-dirs   建立本地目录的目录层次结构
 --crlf          上传是把LF转变成CRLF
 -f/--fail          连接失败时不显示http错误
 --ftp-create-dirs 如果远程目录不存在，创建远程目录
 --ftp-method [multicwd/nocwd/singlecwd] 控制CWD的使用
 --ftp-pasv      使用 PASV/EPSV 代替端口
 --ftp-skip-pasv-ip 使用PASV的时候,忽略该IP地址
 --ftp-ssl       尝试用 SSL/TLS 来进行ftp数据传输
 --ftp-ssl-reqd  要求用 SSL/TLS 来进行ftp数据传输
 -F/--form <name=content> 模拟http表单提交数据
 -form-string <name=string> 模拟http表单提交数据
 -g/--globoff 禁用网址序列和范围使用{}和[]
 -G/--get 以get的方式来发送数据
 -h/--help 帮助
 -H/--header <line>自定义头信息传递给服务器
 --ignore-content-length  忽略的HTTP头信息的长度
 -i/--include 输出时包括protocol头信息
 -I/--head  只显示文档信息
 从文件中读取-j/--junk-session-cookies忽略会话Cookie
 - 界面<interface>指定网络接口/地址使用
 - krb4 <级别>启用与指定的安全级别krb4
 -j/--junk-session-cookies 读取文件进忽略session cookie
 --interface <interface> 使用指定网络接口/地址
 --krb4 <level>  使用指定安全级别的krb4
 -k/--insecure 允许不使用证书到SSL站点
 -K/--config  指定的配置文件读取
 -l/--list-only 列出ftp目录下的文件名称
 --limit-rate <rate> 设置传输速度
 --local-port<NUM> 强制使用本地端口号
 -m/--max-time <seconds> 设置最大传输时间
 --max-redirs <num> 设置最大读取的目录数
 --max-filesize <bytes> 设置最大下载的文件总量
 -M/--manual  显示全手动
 -n/--netrc 从netrc文件中读取用户名和密码
 --netrc-optional 使用 .netrc 或者 URL来覆盖-n
 --ntlm          使用 HTTP NTLM 身份验证
 -N/--no-buffer 禁用缓冲输出
 -o/--output 把输出写到该文件中
 -O/--remote-name 把输出写到该文件中，保留远程文件的文件名
 -p/--proxytunnel   使用HTTP代理
 --proxy-anyauth 选择任一代理身份验证方法
 --proxy-basic   在代理上使用基本身份验证
 --proxy-digest  在代理上使用数字身份验证
 --proxy-ntlm    在代理上使用ntlm身份验证
 -P/--ftp-port <address> 使用端口地址，而不是使用PASV
 -Q/--quote <cmd>文件传输前，发送命令到服务器
 -r/--range <range>检索来自HTTP/1.1或FTP服务器字节范围
 --range-file 读取（SSL）的随机文件
 -R/--remote-time   在本地生成文件时，保留远程文件时间
 --retry <num>   传输出现问题时，重试的次数
 --retry-delay <seconds>  传输出现问题时，设置重试间隔时间
 --retry-max-time <seconds> 传输出现问题时，设置最大重试时间
 -s/--silent静音模式。不输出任何东西
 -S/--show-error   显示错误
 --socks4 <host[:port]> 用socks4代理给定主机和端口
 --socks5 <host[:port]> 用socks5代理给定主机和端口
 --stderr <file>
 -t/--telnet-option <OPT=val> Telnet选项设置
 --trace <file>  对指定文件进行debug
 --trace-ascii <file> Like --跟踪但没有hex输出
 --trace-time    跟踪/详细输出时，添加时间戳
 -T/--upload-file <file> 上传文件
 --url <URL>     Spet URL to work with
 -u/--user <user[:password]>设置服务器的用户和密码
 -U/--proxy-user <user[:password]>设置代理用户名和密码
 -v/--verbose
 -V/--version 显示版本信息
 -w/--write-out [format]什么输出完成后
 -x/--proxy <host[:port]>在给定的端口上使用HTTP代理
 -X/--request <command>指定什么命令
 -y/--speed-time 放弃限速所要的时间。默认为30
 -Y/--speed-limit 停止传输速度的限制，速度时间'秒
 -z/--time-cond  传送时间设置
 -0/--http1.0  使用HTTP 1.0
 -1/--tlsv1  使用TLSv1（SSL）
 -2/--sslv2 使用SSLv2的（SSL）
 -3/--sslv3         使用的SSLv3（SSL）
 --3p-quote      like -Q for the source URL for 3rd party transfer
 --3p-url        使用url，进行第三方传送
 --3p-user       使用用户名和密码，进行第三方传送
 -4/--ipv4   使用IP4
 -6/--ipv6   使用IP6
 -#/--progress-bar 用进度条显示当前的传送状态
```



## 参考链接

- [curl 的用法指南](http://www.ruanyifeng.com/blog/2019/09/curl-reference.html)
- [curl网站开发指南](https://www.ruanyifeng.com/blog/2011/09/curl.html)
- [curl linux 命令 在线中文手册](http://linux.51yip.com/search/curl)
