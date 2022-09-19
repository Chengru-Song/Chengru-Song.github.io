---
layout: article
title: 【ES】 Elastic Search
aside:
  toc: true
key: elastic_search
date: 2022-07-01 11:18:07 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work, es, basics]
---

# [ES] Query

Basic Concept: [https://logz.io/blog/elasticsearch-queries/](https://logz.io/blog/elasticsearch-queries)

42 query examples: [https://coralogix.com/blog/42-elasticsearch-query-examples-hands-on-tutorial/](https://coralogix.com/blog/42-elasticsearch-query-examples-hands-on-tutorial)

# Concepts

1. Match Query - 返回哪些text中包含搜索的关键词
    1. example query
       
        ```json
        POST employees/_search
        {
          "query": {
            "match": {
              "phrase": {
                "query" : "heuristic"
              }
            }
          }
        }
        ```
        
    2. Search phrase instead of a word
       
        ```json
        POST employees/_search
        {
          "query": {
            "match": {
              "phrase": {
                "query" : "heuristic roots help",
        				"operator" : "AND",
        				"minimum_should_match": 3
              }
            }
          }
        }
        ```
        
    3. Multi Fields Query
       
        ```json
        POST employees/_search
        {
          "query": {
            "multi_match": {
                "query" : "research help"
                , "fields": ["position","phrase"]
            }
          }
        }
        ```
        
    4. Match phrase
       
        ```json
        GET employees/_search
        {
          "query": {
            "match_phrase": {
              "phrase": {
                "query": "roots heuristic coherent",
        				"slop": 1
              }
            }
          }
        }
        ```
        
    5. Match phrase prefix
       
        ```json
        GET employees/_search
        {
        "_source": [ "phrase" ],
          "query": {
            "match_phrase_prefix": {
              "phrase": {
                "query": "roots heuristic co",
        				"max_expansions": 60
              }
            }
          }
        }
        ```
    
2. Term Query - 返回文本中的精确查找
    1. Query exact values
       
        ```json
        POST employees/_search
        {
          "query": {
            "terms": {
              "gender": [
                "female",
                "male"
              ]
            }
          }
        }
        ```
        
    2. Exist query
       
        ```json
        GET employees/_search
        {
            "query": {
                "exists": {
                    "field": "company"
                }
            }
        }
        ```
        
    3. Range query
       
        ```json
        POST employees/_search
        {
            "query": {
                "range" : {
                    "experience" : {
                        "gte" : 5,
                        "lte" : 10
                    }
                }
            }
        }
        ```
        
    4. Id query
       
        ```json
        POST employees/_search
        {
            "query": {
                "ids" : {
                    "values" : ["1", "4"]
                }
            }
        }
        ```
        
    5. Prefix query
       
        ```json
        GET employees/_search
        {
          "query": {
            "prefix": {
              "name": "al"
            }
          }
        }
        ```
        
    6. Wildcard Query
       
        ```json
        GET employees/_search
        {
            "query": {
                "wildcard": {
                    "country": {
                        "value": "c*a"
                    }
                }
            }
        }
        ```
        
    7. Regex query
       
        ```json
        GET employees/_search
        {
          "query": {
            "regexp": {
              "position": "res[a-z]*"
            }
          }
        }
        ```
        
    8. Fuzzy query
       
        ```json
        GET employees/_search
        {
          "query": {
            "fuzzy": {
              "country": {
                "value": "Chnia",
                "fuzziness": "2"
              }
            }
          }
        }
        ```
    
3. Boosting
   
    ```json
    POST employees/_search
    {
        "query": {
            "multi_match" : {
                "query" : "versatile Engineer",
                "fields": ["position^3", "phrase"]
            }
        }
    }
    ```
    
4. Sort
    1. Sort by a field
       
        ```json
        GET employees/_search
        {
           "_source": ["name","experience","salary"], 
          "sort": [
            {
              "experience": {
                "order": "desc"
              }
            }
          ]
        
        }
        ```
        
    2. Sort by multiple field
       
        ```json
        GET employees/_search
        {
          "_source": [
            "name",
            "experience",
            "salary"
          ],
          "sort": [
            {
              "experience": {
                "order": "desc"
              }
            },
            {
              "salary": {
                "order": "desc"
              }
            }
          ]
        }
        ```
    
5. Compound query
    1. Bool 下面一共可以放这些字段
       
       
        | must | The conditions or queries in this must occur in the documents to consider them a match. Also, this contributes to the score value.  Eg: if we keep query A and query B in the must section, each document in the result would satisfy both the queries, ie query A AND query B |
        | --- | --- |
        | should | The conditions/queries should match.   Result = query A OR query B |
        | filter | Same as the must clause, but the score will be ignored |
        | must_not | The conditions/queries specified must not occur in the documents. Scoring is ignored and kept as 0 as the results are ignored. |
        
        ```json
        POST _search
        {
          "query": {
            "bool" : {
              "must" : [],
              "filter": [],
              "must_not" : [],
              "should" : []
            }
          }
        }
        ```
        
    2. Multiple conditions
       
        ```json
        POST employees/_search
        {
          "query": {
            "bool": {
              "must": [
                {
                  "match": {
                    "position": "manager"
                  }
                },
                {
                  "range": {
                    "experience": {
                      "gte": 12
                    }
                  }
                }
              ],
            "should": [
              {
                "match": {
                  "phrase": "versatile"
                }
              }
            ]
            }
          }
        }
        ```
        
    3. Condition combo
       
        如果要查询下面这样的SQL，对应的condition是？
        
        ```sql
        SQL: (company = Yamaha OR company = Yozio ) AND (position = manager OR position = associate ) AND (salary>=100000)
        ```
        
        ```sql
        POST employees/_search
        {
            "query": {
                "bool": {
                    "must": [
                      {
                        "bool": {
                            "should": [{
                                "match": {
                                    "company": "Talane"
                                }
                            }, {
                                "match": {
                                    "company": "Yamaha"
                                }
                            }]
                        }
                    }, 
                    {
                        "bool": {
                            "should": [
                              {
                                "match": {
                                    "position": "manager"
                                }
                            }, {
                                "match": {
                                    "position": "Associate"
                                }
                            }
                            ]
                        }
                    }, {
                        "bool": {
                            "must": [
                              {
                                "range": {
                                  "salary": {
                                    "gte": 100000
                                  }
                                }
                              }
                              ]
                        }
                    }]
                }
            }
        }
        ```
        
    4. Boosting
        1. 
        
        ```sql
        POST  employees/_search
        {
            "query": {
                "boosting" : {
                    "positive" : {
                        "match": {
                          "country": "china"
                        }
                    },
                    "negative" : {
                         "match": {
                          "company": "Talane"
                        }
                    },
                    "negative_boost" : 0.5
                }
            }
        }
        ```
        
    5. Function score
        1. Change the score of search documents by weight
            1. 
            
            ```sql
            GET employees/_search
            {
            "_source": ["position","phrase"], 
              "query": {
                "function_score": {
                  "query": {
                    "match": {
                      "position": "manager"
                    }
                  },
                  "functions": [
                    {
                      "filter": {
                        "match": {
                          "phrase": "coherent"
                        }
                      },
                      "weight": 2
                    },
                    {
                      "filter": {
                        "match": {
                          "phrase": "emulation"
                        }
                      },
                      "weight": 10
                    }
                  ],
                  "score_mode": "multiply", 
                  "boost": "5",
                  "boost_mode": "multiply"
                }
              }
            }
            ```