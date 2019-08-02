## Python 爬虫没有思路？可以看看这篇文章





可以说爬虫是学习 Python 的入门必修课。当能独立写出第一个完整的爬虫的时候，我们已经迈出了一大步。因为在这过程中，我们已经学会了如何查看文档，学会使用 Python 相关库的操作，怎样使用 Chrome 的开发者工具（相关工具）和把抓取的数据保存到数据库中等等一系列操作，当然收获最多的还是学习 Python 的自信心。

下面以抓取知乎图片为例，迈出我们自学 Python 爬虫的第一步。

整体思路



在浏览知乎的时候，经常会看到一些有趣的问题，例如有什么万能表情包？，回答中网友分享了收藏多年的表情。但是近1500多条回答，手动保存表情不现实，所以使用爬虫帮我们完成这项工作。

首先我们需要做的是收集此类问题。获取问题的 ID（URL 中 question 后的 ID），构建爬虫任务池。收集问题的工作可以交给爬虫，但是为了抓取的图片都符合预期，建议先手动收集此类问题。然后将问题的 ID 存入数据库中，爬虫运行时读库获取需要爬取的目标贴。通过写一个爬虫，生成任务池多线程调用。将获取到的答案数据清洗，只收集答案中的图片。本案例我们使用 mongo 数据库，因为 mongo 数据库简单方便，也方便我们之后增减字段。

存入库中的结构和知乎的类似：答案集合保存所有答案，每一个答案是一条独立的文档。

**具体类似这种结构：**

```json
{

    
"Answer"
: [

        {

            
"id"
: 
1
,

            
"question_id"
: 
"xxx"
,

            
"title"
: 
"xxx"
,

            
"author"
: 
"xxx"
,

            
"
content: [

                
"imgUrl"
,

                
"..."

            ]

        },

        {

            
"id"
: 
2
,

            
"question_id"
: 
"xxx"
,

            
"title"
: 
"xxx"
,

            
"author"
: 
"xxx"
,

            
"
content: [

                
"imgUrl"
,

                
"..."

            ]

        },

        ...

    ]

}
```

其中 `Answer` 为数据库的集合，集合中的每一项是一条回答。用这种格式保存的好处是我们只需通过贴子的 ID 查询答案即可，保存起来也非常方便。

#### 知乎爬虫

**开始之前**

在开始之前需要安装项目依赖，只用到两个常用库：

```
python3 -m pip install requests pymongo
```

分别用来请求 URL 和操作数据库。安装完成后记得启动 `mongo` 服务。

## **Spider**

爬虫代码比较简单，关键找到知乎答案的接口，解析即可。而且这个接口也是非常好找的。此处用到 Chrome 的开发者工具。

![img](https://mmbiz.qpic.cn/mmbiz_png/LGh7bn8KbYD04rDlXvE6HIG6fuCzgdoX6giaibMFa95xeVy6D0gNhj9HiaB5ickBZ3DwpybPu27s9QTic9nglrsckcQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

接口也没有任何加密或权限限制，在请求头中加入 `Cookies` 就可以了。

通过 Chrome 开发者工具分析，请求携带了如下参数，我们只用到 `limit` 和 `offset`，用来控制接口返回的数量和返回的位置。

```
include: data[*].is_normal,admin_closed_comment,reward_info,is_collapsed,annotation_action,annotation_detail,collapse_reason,is_sticky,collapsed_by,suggest_edit,comment_count,can_comment,content,editable_content,voteup_count,reshipment_settings,comment_permission,created_time,updated_time,review_info,relevant_info,question,excerpt,relationship.is_authorized,is_author,voting,is_thanked,is_nothelp,is_labeled,is_recognized,paid_info;data[*].mark_infos[*].url;data[*].author.follower_count,badge[*].topics
offset:
limit: 3
sort_by: default
platform: desktop
```

完整的请求 URL 是：

```
https://www.zhihu.com/api/v4/questions/21115811/answers?include=data%5B*%5D.is_normal%2Cadmin_closed_comment%2Creward_info%2Cis_collapsed%2Cannotation_action%2Cannotation_detail%2Ccollapse_reason%2Cis_sticky%2Ccollapsed_by%2Csuggest_edit%2Ccomment_count%2Ccan_comment%2Ccontent%2Ceditable_content%2Cvoteup_count%2Creshipment_settings%2Ccomment_permission%2Ccreated_time%2Cupdated_time%2Creview_info%2Crelevant_info%2Cquestion%2Cexcerpt%2Crelationship.is_authorized%2Cis_author%2Cvoting%2Cis_thanked%2Cis_nothelp%2Cis_labeled%2Cis_recognized%2Cpaid_info%3Bdata%5B*%5D.mark_infos%5B*%5D.url%3Bdata%5B*%5D.author.follower_count%2Cbadge%5B*%5D.topics&offset=&limit=3&sort_by=default&platform=desktop
```

只要动态更改其中的 `question`， `limit` 和 `offset` 就可以了。通过接口返回的答案总数，判断需要翻多少页，当然也可以通过接口返回的 `next` 和 `previous` 来获取下一页或前一页答案链接。

当然在翻页抓取的时候切记设置睡眠时间，放在服务器上爬的慢一点也没关系。我们控制好频率，**不要影响到知乎的正常服务**。

请求成功后我们就可以根据自己的需求，存储数据了，至于如何判断答案中是否有图片，可以参考以下代码。

使用到了 `lxml` 库，也可以使用 `re` 库代替。

```python
# 部分代码
@staticmethod
def parse_content(content):    
    """解析答案中的 content，直接获取图片"""    
    if "<img " in content:        
        img_list = set(etree.HTML(content).xpath("//img/@data-original"))        	return list(img_list)    
    else:        
        return []
```

先判断回答中是否有 `img` 标签，如果没有直接返回空列表，如果有的话，通过 `data-original` 属性获取原始大小的图片链接。也是返回一个包含图片链接的列表。

在入库的时候，我们通过 `parse_content` 的返回判断是否需要入库，如果是 `[]` 就跳过，如果列表不为空就入库。这样在之后根据作者展示数据的时候不会只显示作者信息，却没有回答的情况了（其实是该作者回答中只有问题没有图片）。

## **调用爬虫**

当我们完成上述操作，就可以单独写一个文件使用多线程调用爬虫了。

```python
from concurrent.futures import ThreadPoolExecutor
qid_list = db.get_all_question()
crawler_list = [ZhihuSpider(qid).run for qid in qid_list]
with ThreadPoolExecutor(max_workers=4) as executor:  
    future = [executor.submit(task) for task in crawler_list]    
    for f in future:        
        f.result()
```

`qid_list` 来自查库获取所有的知乎贴子 ID。使用 `concurrent.futures` 库并行执行任务，在我们的代码里使用 `ThreadPoolExecutor`，它使用线程池来异步执行调用。 `max_workers` 控制最多线程的使用，本例中使用最多4个线程执行任务。 `ThreadPoolExecutor` 让我们使用多线程时更加简单。具体文档见 ThreadPoolExecutor。

#### 总结

项目的完整代码见 Github: 
https://github.com/alpha87/zhihuFish。

代码比较简单，项目只要思路明确，分而治之，实现了所有的小部分，也就实现了整个项目的大部分。

最后总结一下整体思路：我们先把需要抓取的 ID 存入数据库，作为任务池，爬虫调取任务池中的 ID，将爬取结果再保存到数据库。

等我们学会了 Python 爬虫，再学 Web 开发的时候，还能将我们抓取的结果展示出来，做一个表情包大全网站。一不小心又学习了 Python Web 开发，多好。