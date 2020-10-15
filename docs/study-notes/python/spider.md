# 爬虫初体验（requests）

> 首先你得装requests：

```bash
pip install requests
```

***这里分享一个镜像网站：https://pypi.tuna.tsinghua.edu.cn/simple （清华源）***

> 接着你得引入requests：`import requests`

> 然后你得知道requests的几个常用的方法爬数据

| 名称 | 作用                  |
| ---- | --------------------- |
| get  | 以get请求方式爬取数据 |
| post | 以post请求方式爬数据  |



> 接着你用一个对象存储了爬虫数据

```python
r = requests.get('https://www.bilibili.com')
```

***然而这并不是你想要的那种数据，（只有文本或图片等），它返回的是这个网站的html源码（也就是说之后你得进行数据筛选）***

> 之后你又知道了几种显示数据的方法

| 格式          | 说明                                          |
| ------------- | --------------------------------------------- |
| r.text        | html源码                                      |
| r.content     | html源码，（Unicode型）                       |
| r.status_code | 状态码                                        |
| r.encoding    | 网页内容编码，可以使其变为指定编码（如utf-8） |



> 结果你爬太多了，别人采用了反爬虫技术，你之后爬的就只有空的。这时你就得会反反爬虫技术

eg.

```python
# 通过写入请求头，防止网站的反爬虫技术
header = {'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.102 Safari/537.36'}
r = requests.get('https://www.douban.com', headers = header)
print(r.text)
```

> 咱们还看几个参数**（除了headers）**

````python
r = requests.post('https://www.bilibili.com', cookies = {}) #也是一个dic值
r.history 	# 可以查看重定向
r.url	#查看重定向的url
r = requests.get('https://www.bilibili.com', timeout = 3) # 单位为秒，指过了多少时间之后就停止请求
````

> 你要想保持保持登录状态，可以使用会话

```python
s = requests.session()
```

> 你也阔以使用ip代理来逃过反爬虫机制

```python
proxies = {
    'http': 'http://10.1.10.12:89' 		# 打个比方
}
r = requests.get('https://www.bilibili.com', proxies = proxies) 
```



# XPath的语法及使用

> 前面我们已经说到了requests爬取的数据是需要一定的筛选的，那咋筛选咧：XPath来了。

- 首先装个lxml

  ````bash
  pip install lxml
  ````

- 然后引入：`from lxml import etree`



> 好了，我们开始干吧！

- 先创建一个选择器吧

- 然后选中要筛选的数据标签

  ```python
  selector = etree.HTML(r.text)  # r是一个html的源码（格式还不知道）
  all_li = selector.xpath('//div/ul/li')	# //代表从根节点开始查找，然后找到想要的元素（这里是所有的li元素)
  ```

- 进一步的筛选：

```python
li_1 = selector.xpath('//div/ul/li[1]') 	#找到第一个li
```

​			==这里的`[]`并不是从索引0开始的，而是1==

> 但是你返回的是一个我不能使用的`Element li at 0x2142adf43dac5`这种内存地址的形式，我怎么搞到我想要的数据呢

*其实很简单*：

```python
a_text = selector.xpath('//div/ul/li[1]/a/text()')	#这样就拿到了a标签的文本内容
```

​		==这里的返回值是一个list==，可以适当转换类型，或者：

```python
a_text = selector.xpath('//div/ul/li[1]/a/text()')[0]
```

> 你可能会想，只有标签怎么搞啊，emmm，当然它会有属性选择

```python
a_text = selector.xpath('//div/ul/li[@class = "current"]/a/text()')[0]
```

*格式是：@属性名 = “属性值”*

> 还有属性的值也可以提取出来

```python
c1 = selector.xpath('//li[3]/a/@href')	# 就可以提取出属性值
```

图片啥url的就可以这么搞



# 高级一点的xpath选择器

> 先来一个部分的属性选择器，（开始有点难记了）

```python
all_c = selector.xpath("//li[starts-with(@class, 'item-')]")
```

> 要是咱们就是要一次性的把一个标签（包括子标签）下面的所有text全提取出来怎么搞

string语法：

````python
ul_text = selector.xpath("string(//ul)")	# 使用string()提取全部文本
````



# 咱们看个成功的案例

```python
import requests
from lxml import etree

r = requests.get('https://wallhaven.cc/')
r.encoding = 'utf-8'
print(r.content)
selector = etree.HTML(r.content)
img_top = selector.xpath('//*[@id="featured"]/div[3]/span/a/img/@src')
print(img_top)
for img in img_top:
    print(img)
    re = requests.get(img)
    url = img[30:]
    with open('img/' + url, 'wb') as f:
        f.write(re.content)
```

*注：你得在根目录下建立一个img/文件夹*

