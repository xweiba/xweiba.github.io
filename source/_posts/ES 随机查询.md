---
title: ES 随机查询
date: 2022-12-23 12:50:04
tags:
 - ES
 - 随机查询
categories:
 - ES
---

根据官方文档使用随机评分查询, 随机因子设置为当前时间, 即可实现指定条件的随机数据查询.

参考文档:
>[ELK第七篇:spring-boot-starter-data-elasticsearch使用](https://blog.csdn.net/zjcjava/article/details/78691178)  
>[es官方文档-随机评分](https://www.elastic.co/guide/cn/elasticsearch/guide/current/random-scoring.html)

# es 随机查询
查询语句:
```
{
  "query": {
    "function_score": {
      "query": {
        "bool": {
          "must": [
            {
              "term": {
                "typeCode": "01"
              }
            },
            {
              "term": {
                "gradeCode": "3101"
              }
            }
          ],
          "must_not": [],
          "should": []
        }
      },
      "functions": [
        {
          "random_score": {
            "seed": "15:38:48"
          }
        }
      ],
      "score_mode": "sum"
    }
  },
  "size": 1,
  "from": 0
}
```

# Java 实现

maven 依赖:
```maven
<dependency>
	  <groupId>io.searchbox</groupId>
	  <artifactId>jest</artifactId>
	  <version>5.3.3</version>
</dependency>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>
		spring-boot-starter-data-elasticsearch
	</artifactId>
</dependency>
```

spring boot 配置:
```yml
spring:
  elasticsearch:
    jest:
      uris: http://xxxx:9200
      username: xxx
      password: xxx
      read-timeout: 20000
      connection-timeout: 20000
    content:
      index: xxx
    enable: true
```

test code:
```java
@Autowired
public JestClient jestClient;
	
public List<EsInfoPO> recommendationQuestion(Integer count) {
	List<EsInfoPO> retList = new ArrayList<EsInfoPO>();

	FunctionScoreQueryBuilder functionScoreQueryBuilder =
			QueryBuilders.functionScoreQuery(
					boolQuery().must(QueryBuilders.matchQuery("typeCode", "01")), ScoreFunctionBuilders.randomFunction(System.currentTimeMillis()));

	SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
	searchSourceBuilder.query(functionScoreQueryBuilder).size(count);
	Search search = new Search.Builder(searchSourceBuilder.toString()).addIndex(getIndexName()).addType(getTypeName()).build();

	SearchResult execute = null;

	try {
		execute = jestClient.execute(search);
		if (execute.isSucceeded()) {
			List<SearchResult.Hit<EsInfoPO, Void>> hits = execute.getHits(getClazz());
			for (Hit<EsInfoPO, Void> hit : hits) {
				retList.add(hit.source);
			}
		}
	} catch (Exception e) {
		e.printStackTrace();
	}

	return retList;
}
```