[//]: # (awk分析nginx访问日志)

### awk 简介

基本上来说，awk 可以从输入（ 标准输入，或一个或多个文件 ）中是否存在指定模式的记录（ 即文本行 ）。每次发现匹配时，就执行相关联的动作（ 例如写入到标准输出或外部文件 ）。

### awk 语法

由于我们在 IBM HTTP Server 配置文件中指定了访问日志的固定格式，因此，我们可以轻易地使用 awk 解析，抽取我们需要的数据。

以下面的示例日志为例：

```
 202.189.63.115 - - [31/Aug/2012:15:42:31 +0800] "GET / HTTP/1.1" 200 1365 "-" 

 "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:15.0) Gecko/20100101 Firefox/15.0.1"
```

$0 就是整个记录行

$1 就是访问 IP ” 202.189.63.115”

$4 就是请求时间的前半部分 “[31/Aug/2012:15:42:31”

$5 就是请求时间的后半部分 “+0800]”

以此类推……

当我们使用默认的域分割符时，我们可以从日志中解析出下面不同类型的信息：

 awk '{print $1}' access.log       # IP 地址  (%h) 

 awk '{print $2}' access.log       # RFC 1413 标识  (%l) 

 awk '{print $3}' access.log       # 用户 ID  (%u) 

 awk '{print $4,$5}' access.log     # 日期和时间  (%t) 

 awk '{print $7}' access _log      #  URI (%>s) 

 awk '{print $9}' access _log      # 状态码 (%>s) 

 awk '{print $10}' access _log     # 响应大小  (%b)

我们不难发现，仅使用默认的域分隔符，不方便解析出请求行、引用页和浏览器类型等其他信息，因为这些信息之中包含不确定个数的空格。因此，我们需要把域分隔符修改为 “ ，就能够轻松读出这些信息。

 awk -F\" '{print $2}' access.log        # 请求行 (%r) 

 awk -F\" '{print $4}' access.log        # 引用页 

 awk -F\" '{print $6}' access.log        # 浏览器

注意：这里为了避免 Unix/Linux Shell 误解 “ 为字符串开始，我们使用了反斜杠，转义了 “ 。


### awk 使用

#### 统计浏览器类型

如果我们想知道那些类型的浏览器访问过网站，并按出现的次数倒序排列，我可以使用下面的命令：
```bash
 awk -F\" '{print $6}' access.log | sort | uniq -c | sort -fr
```
此命令行首先解析出浏览器域，然后使用管道将输出作为第一个 sort 命令的输入。第一个 sort 命令主要是为了方便 uniq 命令统计出不同浏览器出现的次数。最后一个 sort 命令将把之前的统计结果倒序排列并输出。

#### 发现系统存在的问题

我们可以使用下面的命令行，统计服务器返回的状态码，发现系统可能存在的问题。
```bash
 awk '{print $9}' access.log | sort | uniq -c | sort
```

### 有关状态码的 awk 命令示例：

#### 查找并显示所有状态码为 404 的请求

 awk '($9 ~ /404/)' access.log

#### 统计所有状态码为 404 的请求

 awk '($9 ~ /404/)' access.log | awk '{print $9,$7}' | sort

现在我们假设某个请求 ( 例如 : URI: /path/to/notfound ) 产生了大量的 404 错误，我们可以通过下面的命令找到这个请求是来自于哪一个引用页，和来自于什么浏览器。

 awk -F\" '($2 ~ "^GET /path/to/notfound "){print $4,$6}' access.log

#### 追查谁在盗链网站图片

系统管理员有时候会发现其他网站出于某种原因，在他们的网站上使用保存在自己网站上的图片。如果您想知道究竟是谁未经授权使用自己网站上的图片，我们可以使用下面的命令：

 awk -F\" '($2 ~ /\.(jpg|gif|png)/ && $4 !~ /^http:\/\/www\.example\.com/)\ 

 {print $4}' access.log \ | sort | uniq -c | sort

 ### 参考

[https://blog.csdn.net/weixin_34026484/article/details/92863078](https://blog.csdn.net/weixin_34026484/article/details/92863078)