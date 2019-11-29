# 爬虫基础
## 本机headers信息
```
headers = {
        "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 10_3 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) CriOS/56.0.2924.75 Mobile/14E5239e Safari/602.1"
    }
    
headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36"
    }

```

## 对称加密与非对称加密
- 对称加密
![](/assets/对称加密.jpg)

- 非对称加密
![](/assets/非对称加密.jpg)

## requests实例
- 先获取单页内容

```
import requests
from requests import RequestException


def get_one_page(url, headers):
    try:
        response = requests.get(url)
        if response.status_code == 200:
            response = response.content
            response = str(response, "gb2312").encode().decode()
            return response
        return None
    except RequestException as e:
        return None


def main():
    url = "http://www.neihanpa.com/article/list_5_1.html"
    headers = {
        "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 10_3 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) CriOS/56.0.2924.75 Mobile/14E5239e Safari/602.1"
    }
    html = get_one_page(url, headers=headers)
    print(html)


if __name__ == '__main__':
    main()
```

# selenium和phantomJS

## PhantomJS（爬取动态页面需要用到）

基于webkit的javaScript API。提供了css选择器，提供了处理文件的I/O操作，支持Web标准、DOM操作、JSON、HTML5、Canvas、SVG。

### mac安装配置：

1.需下载phantomjs-2.1.1-macosx
下载网址：http://phantomjs.org/download.html

2.配置环境变量

```
$ vim ~/.bash_profile
```
在文件中加入安装的phantomjs的路径

```
# Setting PATH for phantomjs-2.1.1-macosx
export PATH="$PATH:/usr/local/Cellar/phantomjs-2.1.1-macosx/bin"

```

3.查看环境变量是否配置成功

```
$ echo $PATH

sbin:/usr/local/Cellar/phantomjs-2.1.1-macosx/bin
```

## Selenium（浏览器自动化测试框架）

定义：Selenium是一个用于Web应用程序测试的工具。Selenium测试直接运行在浏览器中，就像真正的用户在操作一样。

功能：框架底层使用JavaScript模拟真实用户对浏览器进行操作。测试脚本进行时，浏览器自动按照脚本代码做出点击、输入、打开、验证等操作，就像真实用户所做的一样，从终端用户的角度测试应用程序。

### Selenium（mac）安装：

1.命令行安装

```
pip3  install  selenium
```

### Chrome驱动（mac）下载及配置：

1.下载对应版本的浏览器驱动  
下载地址:http://docs.seleniumhq.org/download/

2.解压后得到驱动的可执行文件，将其拷贝到任意环境变量目录
将解压的chromedriver移动到/usr/local/bin目录下，即环境变量下；
