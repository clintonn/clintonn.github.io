---
title: Learning Redis from scratch
date: 2017-04-19 12:45:00 Z
categories:
- code
layout: post
subtitle: NoSQL, not now, not ever
---

![redis logo](http://www.cloudtalk.it/wp-content/uploads/2015/04/redis-database-intervista-sanfilippo.png)

More and more I've been finding roles that ask you to know some NoSQL database implementation — Mongo, Redis, Cassandra, and so forth.

The why gets us into some very thick weeds. [Couchbase][whitepaper], the maker of an open-source document-oriented NoSQL database, has a white paper on why NoSQL databases might be better for certain applications. In short:

1. They're schema-less, which means that your model can expand to fit however many attributes you want instead of sticking to a static schema.
2. You can store more types of data primitives in NoSQL databases. In most cases like Redis, they'll be serialized as a string and can be cast back when you retrieve it.
3. NoSQL databases work well with [horizontal scaling][horizontal-scaling]. They're designed to distribute loads between multiple machines more effectively than RDBMS's, which usually opt for vertical scalability.

There are obviously big discussions out RDBMS and NoSQL DBs that mostly boil down to one question: what are you actually doing with your data? If you're finding relations and doing calculations and joining your data, you'll want a RDBMS. If you just need a document-based store with the ability to save large chunks of binary-safe data, then NoSQL might be a better performer.

In any case, I wasn't out to make any production-scale apps with this database. I simply wanted to get to understand how it worked a bit better.

### Basics

Redis is a data structure store. It stores all its values in unique keys, via the `SET` and `GET` methods for normal primitives (strings, integers, floats) and specialized methods for lists, hashes, and sets.

Most languages come with packages that bind Redis commands to a client. Ruby has its own redis gem, which is structured similarly to what you might see with ActiveRecord:

```ruby
require 'redis'
redis = Redis.new
redis.set("message", "hello world!")
redis.get("message") #=> "hello world!"
```

### More Beyond the Basics

Because life is a little more complicated than strings and numbers, we can add things in sets and lists. Sets are unordered collections of **unique** strings, while lists are ordered by insertion with the ability to add new elements at the head or tail.

```ruby
redis.sadd("girl_names", ["Sally", "Jane", "Emily"])
redis.smembers("girl_names") #=> ["Sally", "Jane", "Emily"]
redis.lpush("boy_names", ["Joe", "John"])
redis.rpush("boy_names", "Jim")
redis.lrange("boy_names", 0, -1) #=> ["John", "Joe", "Jim"]
```

Redis also has support for hashes, and the redis gem has in-built support for native Ruby hashes.

```ruby
redis.mapped_hmset("user:1", { :name => "Clinton Nguyen", :age => 24 })
redis.hgetall("user:1") # => {"name"=>"Clinton Nguyen", "age"=>"24"}
```

Unfortunately everything comes back as a string, so you'll have to recast anything you need back into their requisite datatypes.

### Geospatial Data

For one of my coding challenges, I had to keep track of a set of Marvel heroes arbitrarily assigned to random US cities. Redis has in-built support for geopositions via the `GEOADD` command. This command makes a sorted set and takes in a key name, and multiple 3-tuples containing the name of the member, and its longitude and latitude.

The redis gem's built-in geoadd method can take in an ll array.

```ruby
def geoadd_chars(chars)
  chars.each do |character|
    DB::geoadd("hero_locations", JSON.parse(character["location"]["ll"]), character["name"])
    DB::set(character["name"], character.to_json)
  end
end
```

Redis also has methods for calculating the distance between set members with the `GEORADIUS` command. For instance, inserting your own coordinates in the set, you can figure out your distance compared to any other member of the set using the `GEODIST` command.

### Conclusions

All in all, on a small scale, redis provides a neat and simplistic store for data that can be serialized back and forth. I'd wager that once your concerns start overlapping with model relations, it's time to re-evaluate why you're using NoSQL over any other RDBMS's.

But I can see the power of this when you're handling large documents or otherwise need to handle blobs of data. There's nothing quite so similar in relational database land.

[whitepaper]: https://www.couchbase.com/nosql-resources/why-nosql
[horizontal-scaling]: https://dba.stackexchange.com/posts/4512/revisions
