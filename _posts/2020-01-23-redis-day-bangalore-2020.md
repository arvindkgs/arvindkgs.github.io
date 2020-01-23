---
layout: post
sub_title: Talks and Articles
elements:
- article
- conference
- redis
last_modified_at: 2020-01-23 09:31:57 +0000
categories: []
title: Redis day Bangalore 2020

---
### Thoughts - Redis Day Bangalore 2020

Redis Day Bangalore 2020 was a whirlwind experience. This is a brief summary of the two days. The official home for Redis Day Bangalore  - [https://connect.redislabs.com/redisdaybangalore](https://connect.redislabs.com/redisdaybangalore "https://connect.redislabs.com/redisdaybangalore")

### DAY 1 (Jan 21st)

I thought Redis was a tool for caching and only an alternative for memcache. I couldn’t be more off-target. I was blown away by the multitude of tools/features under Redis.

This day was all about introduction and training on different modules offered by Redis.

1. Redis Streaming Architectures
   * Introduction to Redis streaming.
   * Hands-on session on Creating streams.
   * Creating consumer groups
2. Redis Data Modeling & Patterns
   * Sets
   * Sorted Sets
   * Bit field manipulation - Using Bits to record events in per min/per day
3. Redis Clustering
   * Hashing to divide the keys among the Redis nodes
   * Using Redis Open source cluster
   * Comparison to Redis Enterprise
4. RedisTimeSeries OR RediSearch
   * Introduction to RedisTimeSeries
   * Creating RedisTimeSeries
5. Probabilistic Data Structures
   * Redis P11c Data Structures
   * Bloom Filters

All the session slides are available [here](https://bit.ly/redis-bangalore-slides "here")

### DAY 2 (Jan 22nd)

The second day was more about use-cases of Redis in production by different companies. There was quite a few presentations through out the day. Some of companies that I remember were:

 1. sRide
 2. HolidayMe
 3. VUclip - Using Redis as persistence store
 4. Reliance Jio - Using Redis as a cache
 5. Freshworks - Using Redis for Rate Limiting
 6. Goibibo - Using Redis as persistence store
 7. Myntra - Using Redis for Distributed and fail safe locking
 8. Site24x7
 9. DBS Bank - Using Redis with Pivotal Cloud Foundry
10. Bounce - Using Redis for Geo location data

The slides for above should be available in a few days.

There was hackathon results as well. The top three teams selected from about 12 teams where (ordered by there winning positions)

1. Indexing Videos by text (Converting speech to text) as well as image search in a video
2. Slackbot for monitoring your Redis Cluster
3. Fraud Detection using Redis Bloomfilters