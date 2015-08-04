### Use cases

#### Control your search result using function_score (https://www.elastic.co/guide/en/elasticsearch/reference/1.6/query-dsl-function-score-query.html).

Suppose we have an index called people where we have various information of people, for example age, name, etc.

1. Let's assume peoples' age range is between 1 and 120. Show the result result in the following order

        - Age between 25 and 40 and who live in Austin (both condition matches)
        - Age not in between 25 and 40 but who live in Austin (city matches but not age)
        - Age between 25 and 40 but who do not live in Austin (age matches but not city)
        - Age not in between 25 and 40 and who do not live in Austin (neither matches)

         {
             "query": {
                 "function_score": {
                     "query": {
                         "bool": {
                             "should": [
                                 {
                                     "term": {
                                         "city": {
                                             "value": "Austin"
                                         }
                                     }
                                 },
                                 {
                                     "match_all": {}
                                 }
                             ]
                         }
                     },
                     "boost": 60,
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
                             "boost_factor": 6
                         }
                     ]
                 }
             }
         }

2. Show the result in the following order where living in Austin is a must

        - Age between 25 and 40 and who live in Austin (both condition matches)
        - Age not in between 25 and who live in Austin (city matches but not age)


         {
             "query": {
                 "function_score": {
                     "query": {
                         "bool": {
                             "must": [
                                 {
                                     "term": {
                                         "city": {
                                             "value": "Austin"
                                         }
                                     }
                                 }
                             ]
                         }
                     },
                     "boost": 60,
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
                             "boost_factor": 6
                         }
                     ]
                 }
             }
         }

3. Show the result in the following order where living in Austin is a must

        - Age less than 25 and who live in Austin (both condition matches)
        - Age greater than or equal to 25 and who live in Austin (city matches but not age)


         {
             "query": {
                 "function_score": {
                     "query": {
                         "bool": {
                             "must": [
                                 {
                                     "term": {
                                         "city": {
                                             "value": "Austin"
                                         }
                                     }
                                 }
                             ]
                         }
                     },
                     "boost": 60,
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
                                                     "lt": 25
                                                 }
                                             }
                                         }
                                     ]
                                 }
                             },
                             "boost_factor": 6
                         }
                     ]
                 }
             }
         }

4. Show the result in the following order where living in Austin is a must

        - Age greater than 25 and who live in Austin (both condition matches)
        - Age less than or equal to 25 and who live in Austin (city matches but not age)


         {
             "query": {
                 "function_score": {
                     "query": {
                         "bool": {
                             "must": [
                                 {
                                     "term": {
                                         "city": {
                                             "value": "Austin"
                                         }
                                     }
                                 }
                             ]
                         }
                     },
                     "boost": 60,
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
                                                     "gt": 25
                                                 }
                                             }
                                         }
                                     ]
                                 }
                             },
                             "boost_factor": 6
                         }
                     ]
                 }
             }
         }



5. Show the result in the following order (Example of how to use regular expression query)

		- Name has "awesome" word and who live in Austin (both condition matches)
        - Name does not have "awesome" word but who live in Austin (city matches but not name)
        - Name has "awesome" word but who do not live in Austin (name matches but not city)
        - Name does not have "awesome" word and who do not live in Austin (neither matches)

	    {
	        "query": {
	            "function_score": {
	                "query": {
	                    "bool": {
	                        "should": [
	                            {
	                                "term": {
	                                    "city": {
	                                        "value": "Austin"
	                                    }
	                                }
	                            },
	                            {
	                                "match_all": {}
	                            }
	                        ]
	                    }
	                },
	                "boost": 60,
	                "score_mode": "multiply",
	                "boost_mode": "multiply",
	                "functions": [
	                    {
	                        "filter": {
	                            "bool": {
	                                "should": [
	                                    {
	                                        "regexp": {
	                                            "name": {
	                                                "value": ".*awesome.*|.*Awesome.*"
	                                            }
	                                        }
	                                    }
	                                ]
	                            }
	                        },
	                        "boost_factor": 11
	                    }
	                ]
	            }
	        }
	    }


6. Show the result in the following order (Example of how to use term query)

		- Has car "Honda" and who live in Austin (both condition matches)
        - Doesn't have car "Honda" but who live in Austin (city matches but not car)
        - Has car "Honda" but who do not live in Austin (car matches but not city)
        - Doesn't have car "Honda" and who do not live in Austin (neither matches)

	    {
	        "query": {
	            "function_score": {
	                "query": {
	                    "bool": {
	                        "should": [
	                            {
	                                "term": {
	                                    "city": {
	                                        "value": "Austin"
	                                    }
	                                }
	                            },
	                            {
	                                "match_all": {}
	                            }
	                        ]
	                    }
	                },
	                "boost": 60,
	                "score_mode": "multiply",
	                "boost_mode": "multiply",
	                "functions": [
	                    {
	                        "filter": {
	                            "bool": {
	                                "should": [
	                                    {
	                                        "term": {
	                                            "city": {
	                                                "value": "Honda"
	                                            }
	                                        }
	                                    }
	                                ]
	                            }
	                        },
	                        "boost_factor": 11
	                    }
	                ]
	            }
	        }
	    }



