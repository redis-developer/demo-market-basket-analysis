# Market-Basket Analysis

Market-Basket Analysis(MBA) uses RedisAI and RedisGears to score a market basket. This project allows you to score various baskets based on some pre-populated users and profiles. The data was pulled from [Amazon reviews datasets](https://s3.amazonaws.com/amazon-reviews-pds/readme.html) and the model was built using reviews with verified purchases. Individual user profiles were compiled and analyzed to describe baskets that were trained to build the model that is included in this repo.


## Introduction

In case you're new to Market-Basket Analysis, it is a technique based on buying a group item. The approach is based on the theory that customers who buy a certain item (or group of items) are more likely to buy another specific item (or group of items) It creates If-Then scenario rules, for example, if item A is purchased then item B is likely to be purchased. The rules are probabilistic in nature or, in other words, they are derived from the frequencies of co-occurrence in the observations. Given the historical purchases of a user by category, we would like to know the likelyhood of them purchasing items from other categories.

Market Basket Analysis is also used in fraud detection or to suggest other items that a customer may be interested in purchasing.It may be possible to identify purchase behaviour that can be associated with fraud on the basis of market basket analysis data that contain credit card usage. 

For more information on the topic of Fraud Detection please see our presentation at [Microsoft Developers conference]( https://www.youtube.com/watch?v=UBRuYVn4MjQ)

This demo will allow you to score various baskets based on some pre-populated users and profiles.

## Getting Started

```
docker-compose up
```

Access the demo through your [browser](http://localhost:8080)

1) Select a user to act as
2) View the user profile
3) Click on the cart tab and add new items and score
4) Select a different user and experiment with baskets

### How does it work?

![My Image](https://raw.githubusercontent.com/redis-developer/redis-market-basket-analysis/master/redis-market-basket-analysis.png)

User profiles are stored in Redis as hash data structures. After the user adds items to the cart for scoring, the cart is transformed into a tensor, scored by RedisAI and a confidence score is returned.
 
### Scoring
The scores vary between 0 and 1.  A score closer to 1 indicates that the basket of items by category is more likely to mirror broader purchasing patterns. For example given the another example for a user profile "splurgenarrow" :

 
If we add Wireless, then this is a highly likely basket with a score of 0.99989. Adding items categorized as "wireless" results in a basket with a high likelihood of matching the purchasing patterns that we have modeled from previous data

![My Image](https://raw.githubusercontent.com/redis-developer/redis-market-basket-analysis/master/redis-market-basket-analysis-1.png)

If you monitor, you will see a bunch of Redis hash data structure is being used to add new items as shown below:
 
```
"GET" "session:53ee3f94-c507-4075-a60f-871546e2f90f"
"EXISTS" "USERLIST"
"SMEMBERS" "USERLIST"
...
"SETEX" 
"GET" "session:53ee3f94-c507-4075-a60f-871546e2f90f"
"SETEX" 
..
"GET" "session:53ee3f94-c507-4075-a60f-871546e2f90f"
"HGETALL" "user:splurgenarrow"
```
But if we add Major_Appliances then it will be an unlikely basket with a score of 0.00917

![My Image](https://raw.githubusercontent.com/redis-developer/redis-market-basket-analysis/master/redis-market-basket-analysis-2.png)

```

"GET" "session:53ee3f94-c507-4075-a60f-871546e2f90f"
"AI.TENSORSET" "TENSOR:53ee3f94-c507-4075-a60f-871546e2f90f" "FLOAT" "1" "43" "VALUES" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "2.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "2.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "1.0" "0.0" "0.0" "0.0" "0.0" "0.0" "0.0" "16.0" "0.0" "0.0" "0.0" "0.0" "0.0"
"AI.MODELRUN" "profile_model" "INPUTS" "TENSOR:53ee3f94-c507-4075-a60f-871546e2f90f" "OUTPUTS" "TENSOR:53ee3f94-c507-4075-a60f-871546e2f90f:results"
"AI.TENSORGET" "TENSOR:53ee3f94-c507-4075-a60f-871546e2f90f:results" "META" "BLOB"
"DEL" "TENSOR:53ee3f94-c507-4075-a60f-871546e2f90f"
"DEL" "TENSOR:53ee3f94-c507-4075-a60f-871546e2f90f:results"
"HGETALL" "user:splurgenarrow"
"SETEX" 
```

## Data sources

The data was pulled from Amazon reviews datasets and the model was built using reviews with verified purchases. Individual user profiles were compiled and analyzed to describe baskets that were trained to build the model that is included in this repo.
 


## Building it locally 

Start Docker container

```
docker run -p 6379:6379 redislabs/redismod:edge
```

Setup Environment

```
python -m venv venv
source venv/bin/active
pip install -r requirements.txt
python3 app.py
```


