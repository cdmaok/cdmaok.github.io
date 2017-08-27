---
layout: post
title: ES-quick-note
category: ES
---

## 兼容IK
1. 把elastic-ik打包之后放到__$ES_HOME/plugins/ik__(如果没有，自行建立目录)  
2. 在__$ES_HOME/plugins/ik/config__ 里面配置停用词或者自定义词典等  
3. 重启es即可。  

## 加入同义词
在/config/elas*.yml添加如下代码：  
```
index:
  analysis:
    analyzer:
      ik_syno:
          type: custom
          tokenizer: ik_max_word
          filter: [my_synonym_filter]
      ik_syno_smart:
          type: custom
          tokenizer: ik_smart
          filter: [my_synonym_filter]
    filter:
      my_synonym_filter:
          type: synonym
          synonyms_path: analysis/synonym.txt
```

其中analysis/synonym.txt为同义词典，在config的目录下面，格式大概如下：  
![es_syn_example][es_syn_example]

## 使用
1. 建立文档索引  
2. 添加文档到索引
3. 检索

## 集群配置
配置一个集群还是相对容易的

```
network.host: ip1 # 这一项是绑定公网的ip，否则只有内网才能访问
cluster.name: cdmaok
node.name: node-1 # 各个节点不一样
node.master: true
node.data: true
index.number_of_shards: 5
index.number_of_replicas: 1
discovery.zen.ping.unicast.hosts: ['ip1:port','ip2:port'] # 把集群里面的所有机器的ip都放进去

```

## UI interface

```
elasticsearch/bin/plugin install mobz/elasticsearch-head
open http://localhost:9200/_plugin/head/
```

Note: 最好在配置文件里把**path.data**和**path.log**分别配置出来，不要在$ES_HOME里面。

## Appendix  
1. check ES server if start  
```
curl -XGET http://127.0.0.1:9200/?pretty
```
2. create index  
```
curl -XPUT 'http://localhost:9200/index_name/' -d '
{
    "index": "matchp",
    "type": "weibo",
    "body": {
        "weibo": {
            "properties": {
                "text": {
                    "type": "string",
                    "analyzer": "ik_syno" ## setting annalyer  
                }
            }
        }
    }
}
'
```
3. add document to index  
```
curl -XPUT 'http://localhost:9200/index_name/type/id' -d '{
        "text": "番茄 abc def gdb"
}'
```
4. check if synonym words  
```
curl -XGET 'http://localhost:9200/matchp/_analyze?analyzer=ik_syno' -d '{"text":"西红柿"}'
```
5. search document  
```
curl -XPOST 'http://localhost:9200/index_name/type_name/_search' -d '
{
  "query": {
      "query_string": {
          "text": {
            "query": "西红柿 abc",
            "analyzer": "ik_syno"
            }
      }
  }
}
'
```
6. delete all the index
```
curl -XDELETE 'http://localhost:9200/_all'
```


[es_syn_example]:http://7xpv97.com1.z0.glb.clouddn.com/es_synomn_example.jpg
