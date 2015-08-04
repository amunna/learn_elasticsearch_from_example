### Use cases

#### Control your search result using function_score (https://www.elastic.co/guide/en/elasticsearch/reference/1.6/query-dsl-function-score-query.html).

Suppose we have an index called people where we have various information of people, for example age, name, etc.

1. First show people whose age is between 25 to 40, then show people whose age is between 20 to 24 and 41 to 90, then show people whose age are less than 20 or greater than 90.


		curl -XPOST 'localhost:9200/people/info/_search' -d '
		{
		    "query": {
		        "function_score": {
		            "query": {
		                "bool": {
		                    "should": [
		                        {
		                            "match_all": {}
		                        }
		                    ],
		                    "must": [
		                        {
		                            "range": {
		                                "age": {
		                                    "gte": 20,
		                                    "lte": 90
		                                }
		                            }
		                        }
		                    ]
		                }
		            },
		            "boost": 50,
		            "score_mode": "multiply",
		            "boost_mode": "multiply",
		            "functions": [
		                {
		                    "filter": {
		                        "bool": {
		                            "should": [
		                                {
		                                    "range": {
		                                        "age": {
		                                            "gte": 25,
		                                            "lte": 40
		                                        }
		                                    }
		                                }
		                            ]
		                        }
		                    },
		                    "boost_factor": 4
		                }
		            ]
		        }
		    }
		}'


2. First show people whose age is between 25 to 40, then show people whose age is between 20 to 24 and 41 to 90.


		curl -XPOST 'localhost:9200/people/info/_search' -d '
		{
		    "query": {
		        "function_score": {
		            "query": {
		                "bool": {
		                    "must": [
		                        {
		                            "range": {
		                                "age": {
		                                    "gte": 20,
		                                    "lte": 90
		                                }
		                            }
		                        }
		                    ]
		                }
		            },
		            "boost": 50,
		            "score_mode": "multiply",
		            "boost_mode": "multiply",
		            "functions": [
		                {
		                    "filter": {
		                        "bool": {
		                            "should": [
		                                {
		                                    "range": {
		                                        "age": {
		                                            "gte": 25,
		                                            "lte": 40
		                                        }
		                                    }
		                                }
		                            ]
		                        }
		                    },
		                    "boost_factor": 4
		                }
		            ]
		        }
		    }
		}'

