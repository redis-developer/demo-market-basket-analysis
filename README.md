# Redis Market Basket Analysis

Uses RedisAI to score a market basket

## Use case

Market basket analysis is a technique based on buying a group item. The approach is based on the theory that customers who buy a certain item (or group of items) are more likely to buy another specific item (or group of items) It creates If-Then scenario rules, for example, if item A is purchased then item B is likely to be purchased. The rules are probabilistic in nature or, in other words, they are derived from the frequencies of co-occurrence in the observations.

```If {A} then likelihood of B is probably more accurate```

Given the historical purchases of a user by category, we would like to know the likelyhood of them purchasing items from other categories.

This is called [Market Basket Analysis](https://smartbridge.com/market-basket-analysis-101/)

Market Basket Analysis can be used in fraud detection or to suggest other items that a customer may be interested in purchasing.

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

![My Image](redis-market-basket-analysis.png)

User profiles are stored in Redis as hash data structures. After the user adds items to the cart for scoring, the cart is transformed into a tensor, scored by RedisAI and a confidence score is returned.
 
### Scoring
The scores vary between 0 and 1.  A score closer to 1 indicates that the basket of items by category is more likely to mirror broader purchasing patterns. For example given the another example for a user profile "splurgenarrow" :

 
If we add Wireless, then this is a highly likely basket with a score of 0.99989. Adding items categorized as "wireless" results in a basket with a high likelihood of matching the purchasing patterns that we have modeled from previous data

![My Image](redis-market-basket-analysis-1.png)



## Developing

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

## Architecture

User profiles are stored in Redis as [hash data structures](https://redis.io/commands#hash)

After the user adds items to the cart for scoring, the cart is transformed into a tensor, scored by [RedisAI](https://redisai.io) and a confidence score is returned.

## Data sources

The data was pulled from [Amazon reviews datasets](https://s3.amazonaws.com/amazon-reviews-pds/readme.html) and the model was built using reviews with verified purchases.  Individual user profiles were compiled and analyzed to describe baskets that were trained to build the model that is included in this repo.
