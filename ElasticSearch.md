### ElasticSearch

### 架构选择

**ElasticSearch**

当实时建立索引时，solr会产生阻塞，查询性能较差，es有明显优势

随着数据量的增多，Solr的搜索效率会更低

**solr**

功能更多，更加成熟

单纯对已有数据查询时 更快

随着数据量的增多，Solr的搜索效率会更低

### ElasticSearch

jdk1.8

ElasticSearch 客户端

官网

```
bin 启动文件
config 配置文件
	Log4j2 日志配置文件
	jvm.options java 虚拟机相关的配置
	elasticsearch.ym elasticsearch 的配置文件!默认 9200 端口! 跨域!
Lib 相关 jar包
1ogs    日志!
modules 功能模块
plugins 插件!
```

> 安装可视化界面 ElasticSearch head





### 核心概念

1. 索引
2. 字段类型（mapping）
3. 文档（document）

面向文档的关系行数据库  关系行数据库 和 elasticsearch

| 数据库 | 索引      |
| ------ | --------- |
| 表     | types     |
| 行     | documents |
| 字段   | fields    |

把索引划分为多个发片 

默认名称为elasticSearch

使用**倒排索引** 查找数据

**倒排索引**

1. 将文档进行分词
2. 通过分词出来的词作为key，文档名字作为value，所谓的倒排索引
3. 用户搜索某个key就可以查看包含key的文档的score
4. 用户就能得到可能的文档列表，以出现频率去排序的

### IK

基于restful分词器

```shell
ik_smart # 最少切分

ik_max_word # 最细粒度切分
```

```json
get _analyze
{
	"analyzer": "ik_smart",
	"text": "找工作好难"
}

get _analyze
{
	"analyzer": "ik_max_word",
	"text": "找工作好难"
}
# keyword分析
get _analyze
{
	"analyzer": "keyword",
	"text": "找工作好难"
}
```

通过自定义 dic文件，创建自己的分词库

```
IKAnalyzer.cfg.xml # 引入自带分词器
```



### RestFul

**索引基本操作 **

```json
PUT /test2 创建规则
{
    "mappings":{
        "properties":{
           "name":{
               "type":"text"
            },
            "age":{
                "type":"long"
            },
            "birthday":{
                "type":"date"
            }
        }
    }
}

GET test2 获取test2 具体信息

PUT /test3/doc/1  存数据
{
	"name":"狂神说"，
	"age":13,
	"birth":"1997-01-05"
}
```

**文档基本操作**

```json
Put /xmd/user/1
{
    "name":"小米的",
    "age":22,
    "desc":"taimengla",
    "tag":["爱国","名族"]
}

Put /xmd/user/2
{
    "name":"华为的",
    "age":42,
    "desc":"爱国",
    "tag":["爱国","名族"]
}

Get /xmd/user/1 获取数据  通过id
Put /xmd/user/2 覆盖修改
{
    "name":"华为的",
    "age":42,
    "desc":"爱国",
    "tag":["爱国","名族"]
}   

post xmd/user/1/_update  灵活性更高
{
    "doc":{
        "name":"leijun"
    }
}

# 简单的条件查询
GET xmd/user/_search?q=name:xmd

# 复杂的条件查询  
GET xmd/user/_search
{
    #  where 模糊查询
    "query":{
		# bool查询
		"bool":{
    		# and -> must  or -> should
			# not -> must_not
            "must/should":[
                "match":{
                    "name":"xmd"
                },
                "match":{
                     "age":23
                }
            ] ,
		   "filter":{
               "range":{
                   "age":{
                       "lt":10
                   }
               }
           }
			
        }
    }
     # 结果过滤 select field
    "_source":["name","desc"]
	# 排序 order by
	"sort":[
        {
            "age":{
            	"order":"desc"
            }
        }
    ]
	# 分页  
	"from": 0,
	"size": 2
	# 精确查询
	trim 查询精确的
	# 高亮查询
	highlight:{
    	# 高亮样式 自定义搜索高亮条件
        "pre_tags": "<p class ='key' style = 'color:red'>"
     	"post_tags": "</p>"
        "field":{
            "name":{}
        }
    }
}	

Result：
# 索引和文档的信息 查询的结果总数 查询出来的具体的文档
# score:通过score来判断谁更加符合结果
"hits":[
    {
        "_index":
        "_type":
        "_id":
        "_score":
        "_source":{
            "name":"华为的",
            "age":42,
            "desc":"爱国",
            "tag":["爱国","名族"f]
    	}
    }
    {
    
    }
]
```





### 集成SpringBoot

> 找文档

ES Clients

- 现在有更好的

1. 找到原生依赖
2. 找对象
3. 初始化
4. 分析这个类的方法即可

> 新建项目添加elasticSearch（nosql中 ） 
>
> 问题 ： 一定要保证导入的版本和本地的版本保持一致
>
> maven点刷新

添加config类 相当于xml文件

```java
@Autowired
@Qualifier("restHighLevelClient")
private RestHighLevelClient client


// 创建索引
CreateIndexRequest xmd = new CreateIndexRequest("xmd");
// 客户端执行请求
client.indices().create(create,RequestOptions.Default);


// 添加文档
void AddDocument() throws IOexception{
    User user = new user("xmd",22);
    IndexRequest request = new IndexRequest("xmd");

    // 规则 put /xmd/_doc/1
    request.id("1");
    request.timeout(TimeValue.timesValueSeconds(1)); // 1s过期
    request.timeout("1s");

    // 存放数据
    IndexRequet source =new request.source(user,XContentType.Json) // user对象 装换为json

    // 客户端发送请求
    IndexRequest indexResponse = client.index(request,RequestOptions.Default)
}

// 文档是否存在 get xmd\1
void isExist() throws IOexception{
	GetRequest getRequest = new GetRequest(index:"xmd",id:"1");
    // 不获取返回的_source 的上下文了
    getRequest.fetchSourceContext(new FetchSourceContext(false));
    getRequest.storedFields("_none_");
    client.isExist(getRequest,RequestOptions.Default)
}
// 查看文档 get xmd\1
void isExist() throws IOexception{
	GetRequest getRequest = new GetRequest(index:"xmd",id:"1");
	GetResponse getResponse = client.get(getRequest,RequestOptions.Default);
    sout(getResponse.getSourceToString); //文档内容
    sout(getResponse.getSourceToString);  // 返回全部内容
    // update
    UpdateRequest updateRequest = new UpdateRequest("xmd".id:"1");     User user = new user("xmd",22);
    // TODO: user对象 装换为json
    updateRequest.doc(user,XContentType.Json) 
    client.update(updateRequest)

}

// 获取文档，判断是否存在 get /liuyou_index/_doc/1
@Test
public void testDocumentIsExists() throws IOException {
    GetRequest request = new GetRequest("liuyou_index", "1");
    // 不获取返回的 _source的上下文了
    request.fetchSourceContext(new FetchSourceContext(false));
    request.storedFields("_none_");
    boolean exists = restHighLevelClient.exists(request, RequestOptions.DEFAULT);
    System.out.println(exists);
}

// 测试更新文档内容
@Test
public void testUpdateDocument() throws IOException {
    UpdateRequest request = new UpdateRequest("liuyou_index", "1");
    User user = new User("lmk",11);
    request.doc(JSON.toJSONString(user),XContentType.JSON);
    UpdateResponse response = restHighLevelClient.update(request, RequestOptions.DEFAULT);
    System.out.println(response.status()); // OK
    restHighLevelClient.close();
}

// 测试删除文档
@Test
public void testDeleteDocument() throws IOException {
    DeleteRequest request = new DeleteRequest("liuyou_index", "1");
    request.timeout("1s");
    DeleteResponse response = restHighLevelClient.delete(request, RequestOptions.DEFAULT);
    System.out.println(response.status());// OK
}


// 查询
// SearchRequest 搜索请求
// SearchSourceBuilder 条件构造
// HighlightBuilder 高亮
// TermQueryBuilder 精确查询
// MatchAllQueryBuilder
// xxxQueryBuilder ...
@Test
public void testSearch() throws IOException {
    // 1.创建查询请求对象
    SearchRequest searchRequest = new SearchRequest();
    // 2.构建搜索条件
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    // (1)查询条件 使用QueryBuilders工具类创建
    // 精确查询
    TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "liuyou");
    //        // 匹配查询
    //        MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
    // (2)其他<可有可无>：（可以参考 SearchSourceBuilder 的字段部分）
    // 设置高亮
    searchSourceBuilder.highlighter(new HighlightBuilder());
    //        // 分页
    //        searchSourceBuilder.from();
    //        searchSourceBuilder.size();
    searchSourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
    // (3)条件投入
    searchSourceBuilder.query(termQueryBuilder);
    // 3.添加条件到请求
    searchRequest.source(searchSourceBuilder);
    // 4.客户端查询请求
    SearchResponse search = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);
    // 5.查看返回结果
    SearchHits hits = search.getHits();
    System.out.println(JSON.toJSONString(hits));
    System.out.println("=======================");
    for (SearchHit documentFields : hits.getHits()) {
        System.out.println(documentFields.getSourceAsMap());
    }
}


// 上面的这些api无法批量增加数据（只会保留最后一个source）
@Test
public void test() throws IOException {
    IndexRequest request = new IndexRequest("bulk");// 没有id会自动生成一个随机ID
    request.source(JSON.toJSONString(new User("liu",1)),XContentType.JSON);
    request.source(JSON.toJSONString(new User("min",2)),XContentType.JSON);
    request.source(JSON.toJSONString(new User("kai",3)),XContentType.JSON);
    IndexResponse index = restHighLevelClient.index(request, RequestOptions.DEFAULT);
    System.out.println(index.status());// created
}


// 特殊的，真的项目一般会 批量插入数据
@Test
public void testBulk() throws IOException {
    BulkRequest bulkRequest = new BulkRequest();
    bulkRequest.timeout("10s");
    ArrayList<User> users = new ArrayList<>();
    users.add(new User("liuyou-1",1));
    users.add(new User("liuyou-2",2));
    users.add(new User("liuyou-3",3));
    users.add(new User("liuyou-4",4));
    users.add(new User("liuyou-5",5));
    users.add(new User("liuyou-6",6));
    // 批量请求处理
    for (int i = 0; i < users.size(); i++) {
        bulkRequest.add(
                // 这里是数据信息
                new IndexRequest("bulk")
                        .id(""+(i + 1)) // 没有设置id 会自定生成一个随机id
                        .source(JSON.toJSONString(users.get(i)),XContentType.JSON)
        );
    }
    BulkResponse bulk = restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
    System.out.println(bulk.status());// ok
}
```
