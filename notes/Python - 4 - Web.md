# virtualenv

1. 安装virtualenv

```sh
pip3 install virtualenv
```



2. 进入项目文件夹，创建虚拟环境

   可指定其他 python 版本

```sh
virtualenv -p python3 venv
```



3. 激活环境

```sh
source venv/bin/activate
```



4. 退出环境

```sh
deactivate
```





# urllib





# requests

## Response

`request#` 方法会返回一个 Response 对象，类型为 `<class 'requests.models.Response'>`



### 属性

r.url：请求体的 url

r.text：服务器响应的内容

r.content：服务器响应的内容的二进制字节流

r.json：将 response.text 通过 json 解析器转为字典

r.raw：原始响应内容，获得一个 `urllib3.response.HTTPResponse` 对象

r.status_code：响应状态码

r.reason

r.cookies：返回一个 `requests.cookies.RequestsCookieJar` 对象



### 方法



## Request



## Cookie



## Session

Session 的作用是会话保持，在同一个 Session 实例发出的所有请求之间保持 cookie，所以如果向同一主机发送多个请求，底层的 TCP 连接将会被复用，从而带来显著的性能提升。

Session 在保持登录状态的使用最多，因为不可能每进行一次请求就登陆一次，所以需要使用 Session 保持登录状态。





## 代理





## 超时



# BeautifulSoup4

BeautifulSoup4 是XML 和 HTML 的解析库，可以从中提取数据。



## 解析器

- `html.parse`：python 自带，但容错性不够高，对于一些写得不太规范的网页会丢失部分内容

- `lxml`：解析速度快，需额外安装

- `xml`：同属 lxml 库，支持 XML 文档

- `html5lib`：最好的容错性，但速度稍慢





# Scrapy