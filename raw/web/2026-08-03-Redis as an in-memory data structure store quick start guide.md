---
title: "Redis as an in-memory data structure store quick start guide"
description: "Understand how to use basic Redis data types"
source_url: "https://redis.io/docs/latest/develop/get-started/data-store/"
source_domain: "redis.io"
author:
published:
clipped: 2026-08-03
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Redis as an in-memory data structure store quick start guide

> Source: [Redis as an in-memory data structure store quick start guide](https://redis.io/docs/latest/develop/get-started/data-store/)
> Clipped: 2026-08-03

This quick start guide shows you how to:

1. Get started with Redis
2. Store data under a key in Redis
3. Retrieve data with a key from Redis
4. Scan the keyspace for keys that match a specific pattern

The examples in this article refer to a simple bicycle inventory.

## Setup

The easiest way to get started with Redis is to use Redis Cloud:

1. Create a [free account](https://redis.com/try-free?utm_source=redisio&utm_medium=referral&utm_campaign=2023-09-try_free&utm_content=cu-redis_cloud_users).
	![](https://redis.io/docs/latest/develop/get-started/img/free-cloud-db.png)
2. Follow the instructions to create a free database.

You can alternatively follow the [installation guides](https://redis.io/docs/latest/operate/oss_and_stack/install/install-stack/) to install Redis on your local machine.

## Connect

The first step is to connect to Redis. You can find further details about the connection options in this documentation site's [Tools section](https://redis.io/docs/latest/develop/tools/). The following example shows how to connect to a Redis server that runs on localhost (`-h 127.0.0.1`) and listens on the default port (`-p 6379`):

```redis-cli
> redis-cli -h 127.0.0.1 -p 6379
```

```python
r = redis.Redis(host="localhost", port=6379, db=0, decode_responses=True)
```

```node.js
const client = createClient();
client.on('error', err => console.log('Redis Client Error', err));

await client.connect();
```

```java
RedisClient jedis = RedisClient.create("localhost", 6379);
```

```go
ctx := context.Background()

rdb := redis.NewClient(&redis.Options{
    Addr:     "localhost:6379",
    Password: "", // no password docs
    DB:       0,  // use default DB
    Protocol: 2,
})
```

```c#
var muxer = ConnectionMultiplexer.Connect("localhost:6379");
var db = muxer.GetDatabase();
var ft = db.FT();
var json = db.JSON();
```

## Store and retrieve data

Redis stands for Remote Dictionary Server. You can use the same data types as in your local programming environment but on the server side within Redis.

Similar to byte arrays, Redis strings store sequences of bytes, including text, serialized objects, counter values, and binary arrays. The following example shows you how to set and get a string value:

```python
"""
Code samples for data structure store quickstart pages:
    https://redis.io/docs/latest/develop/get-started/data-store/
"""

import redis

r = redis.Redis(host="localhost", port=6379, db=0, decode_responses=True)

res = r.set("bike:1", "Process 134")
print(res)
# >>> True

res = r.get("bike:1")
print(res)
# >>> "Process 134"
```

```node.js
import { createClient } from 'redis';

const client = createClient();

client.on('error', err => console.log('Redis Client Error', err));

await client.connect().catch(console.error);

await client.set('bike:1', 'Process 134');
const value = await client.get('bike:1');
console.log(value);
// returns 'Process 134'

await client.close();
```

```node.js
import assert from 'node:assert';
import { Redis } from 'ioredis';

const redis = new Redis();

const res1 = await redis.set('bike:1', 'Process 134');
console.log(res1); // >>> OK

const res2 = await redis.get('bike:1');
console.log(res2); // >>> Process 134

redis.disconnect();
```

```java
package io.redis.examples;

import redis.clients.jedis.RedisClient;

public class SetGetExample {

  public void run() {

    RedisClient jedis = RedisClient.create("redis://localhost:6379");

    String status = jedis.set("bike:1", "Process 134");

    if ("OK".equals(status)) System.out.println("Successfully added a bike.");

    String value = jedis.get("bike:1");

    if (value != null) System.out.println("The name of the bike is: " + value + ".");

    jedis.close();
  }
}
```

```java
package io.redis.examples.async;

import io.lettuce.core.RedisClient;
import io.lettuce.core.api.async.RedisAsyncCommands;
import io.lettuce.core.api.StatefulRedisConnection;

import java.util.concurrent.CompletableFuture;

public class SetGetExample {

    public void run() {
        RedisClient redisClient = RedisClient.create("redis://localhost:6379");

        try (StatefulRedisConnection<String, String> connection = redisClient.connect()) {
            RedisAsyncCommands<String, String> asyncCommands = connection.async();

            CompletableFuture<Void> setGetExample = asyncCommands.set("bike:1", "Process 134")
                    .thenCompose(res1 -> {
                        System.out.println(res1); // >>> OK
                        return asyncCommands.get("bike:1");
                    }).thenAccept(res2 -> {
                        System.out.println(res2); // >>> Process 134
                    }).toCompletableFuture();

            setGetExample.join();
        } finally {
            redisClient.shutdown();
        }
    }
}
```

```java
package io.redis.examples.reactive;

import io.lettuce.core.RedisClient;
import io.lettuce.core.api.reactive.RedisReactiveCommands;
import io.lettuce.core.api.StatefulRedisConnection;

import reactor.core.publisher.Mono;

public class SetGetExample {

    public void run() {
        RedisClient redisClient = RedisClient.create("redis://localhost:6379");

        try (StatefulRedisConnection<String, String> connection = redisClient.connect()) {
            RedisReactiveCommands<String, String> reactiveCommands = connection.reactive();

            Mono<Void> setGetExample = reactiveCommands.set("bike:1", "Process 134")
                    .doOnNext(res1 -> {
                        System.out.println(res1); // >>> OK
                    })
                    .then(reactiveCommands.get("bike:1"))
                    .doOnNext(res2 -> {
                        System.out.println(res2); // >>> Process 134
                    })
                    .then();

            setGetExample.block();
        } finally {
            redisClient.shutdown();
        }
    }
}
```

```go
package example_commands_test

import (
    "context"
    "fmt"

    "github.com/redis/go-redis/v9"
)

func ExampleClient_Set_and_get() {
    ctx := context.Background()

    rdb := redis.NewClient(&redis.Options{
        Addr:     "localhost:6379",
        Password: "", // no password docs
        DB:       0,  // use default DB
    })

    err := rdb.Set(ctx, "bike:1", "Process 134", 0).Err()
    if err != nil {
        panic(err)
    }

    fmt.Println("OK")

    value, err := rdb.Get(ctx, "bike:1").Result()
    if err != nil {
        panic(err)
    }
    fmt.Printf("The name of the bike is %s", value)

}
```

```c#
using NRedisStack.Tests;
using StackExchange.Redis;

public class SetGetExample
{
    public void Run()
    {
        var muxer = ConnectionMultiplexer.Connect("localhost:6379");
        var db = muxer.GetDatabase();

        bool status = db.StringSet("bike:1", "Process 134");

        if (status)
            Console.WriteLine("Successfully added a bike.");

        var value = db.StringGet("bike:1");

        if (value.HasValue)
            Console.WriteLine("The name of the bike is: " + value + ".");

    }
}
```

```php
<?php
use Predis\Client as PredisClient;

class SetGetTest
{
    public function testSetGet() {
        $r = new PredisClient([
            'scheme'   => 'tcp',
            'host'     => '127.0.0.1',
            'port'     => 6379,
            'password' => '',
            'database' => 0,
        ]);

        $res1 = $r->set('bike:1', 'Process 134');
        echo $res1 . PHP_EOL; // >>> OK

        $res2 = $r->get('bike:1');
        echo $res2 . PHP_EOL; // >>> Process 134

    }
}
```

```ruby
require 'redis'

r = Redis.new

res1 = r.set('bike:1', 'Process 134')
puts res1 # >>> OK

res2 = r.get('bike:1')
puts res2 # >>> Process 134
```

```rust
mod set_and_get_tests {
    use redis::Commands;

    fn run() {
        let mut r = match redis::Client::open("redis://127.0.0.1") {
            Ok(client) => match client.get_connection() {
                Ok(conn) => conn,
                Err(e) => {
                    println!("Failed to connect to Redis: {e}");
                    return;
                }
            },
            Err(e) => {
                println!("Failed to create Redis client: {e}");
                return;
            }
        };

        if let Ok(res1) = r.set("bike:1", "Process 134") {
            let res1: String = res1;
            println!("{res1}"); // >>> OK
        }

        if let Ok(res2) = r.get("bike:1") {
            let res2: String = res2;
            println!("{res2}"); // >>> Process 134
        }

    }
}
```

```rust
mod set_and_get_tests {
    use redis::AsyncCommands;

    async fn run() {
        let mut r = match redis::Client::open("redis://127.0.0.1") {
            Ok(client) => match client.get_multiplexed_async_connection().await {
                Ok(conn) => conn,
                Err(e) => {
                    println!("Failed to connect to Redis: {e}");
                    return;
                }
            },
            Err(e) => {
                println!("Failed to create Redis client: {e}");
                return;
            }
        };

        if let Ok(res1) = r.set("bike:1", "Process 134").await {
            let res1: String = res1;
            println!("{res1}"); // >>> OK
        }

        if let Ok(res2) = r.get("bike:1").await {
            let res2: String = res2;
            println!("{res2}"); // >>> Process 134
        }

    }
}
```

Hashes are the equivalent of dictionaries (dicts or hash maps). Among other things, you can use hashes to represent plain objects and to store groupings of counters. The following example explains how to set and access field values of an object:

```python
res1 = r.hset(
    "bike:1",
    mapping={
        "model": "Deimos",
        "brand": "Ergonom",
        "type": "Enduro bikes",
        "price": 4972,
    },
)
print(res1)
# >>> 4

res2 = r.hget("bike:1", "model")
print(res2)
# >>> 'Deimos'

res3 = r.hget("bike:1", "price")
print(res3)
# >>> '4972'

res4 = r.hgetall("bike:1")
print(res4)
# >>> {'model': 'Deimos', 'brand': 'Ergonom', 'type': 'Enduro bikes', 'price': '4972'}
```

```node.js
const res1 = await client.hSet(
  'bike:1',
  {
    'model': 'Deimos',
    'brand': 'Ergonom',
    'type': 'Enduro bikes',
    'price': 4972,
  }
)
console.log(res1) // 4

const res2 = await client.hGet('bike:1', 'model')
console.log(res2)  // 'Deimos'

const res3 = await client.hGet('bike:1', 'price')
console.log(res3)  // '4972'

const res4 = await client.hGetAll('bike:1')
console.log(res4)  
/*
{
  brand: 'Ergonom',
  model: 'Deimos',
  price: '4972',
  type: 'Enduro bikes'
}
*/
```

```java
Map<String, String> bike1 = new HashMap<>();
bike1.put("model", "Deimos");
bike1.put("brand", "Ergonom");
bike1.put("type", "Enduro bikes");
bike1.put("price", "4972");

Long res1 = jedis.hset("bike:1", bike1);
System.out.println(res1); // 4

String res2 = jedis.hget("bike:1", "model");
System.out.println(res2); // Deimos

String res3 = jedis.hget("bike:1", "price");
System.out.println(res3); // 4972

Map<String, String> res4 = jedis.hgetAll("bike:1");
System.out.println(res4); // {type=Enduro bikes, brand=Ergonom, price=4972, model=Deimos}
```

```java
Map<String, String> bike1 = new HashMap<>();
bike1.put("model", "Deimos");
bike1.put("brand", "Ergonom");
bike1.put("type", "Enduro bikes");
bike1.put("price", "4972");

CompletableFuture<Void> setGetAll = asyncCommands.hset("bike:1", bike1).thenCompose(res1 -> {
    System.out.println(res1); // >>> 4
    return asyncCommands.hget("bike:1", "model");
}).thenCompose(res2 -> {
    System.out.println(res2); // >>> Deimos
    return asyncCommands.hget("bike:1", "price");
}).thenCompose(res3 -> {
    System.out.println(res3); // >>> 4972
    return asyncCommands.hgetall("bike:1");
})
        .thenAccept(System.out::println)
        // >>> {type=Enduro bikes, brand=Ergonom, price=4972, model=Deimos}
        .toCompletableFuture();
```

```java
Map<String, String> bike1 = new HashMap<>();
bike1.put("model", "Deimos");
bike1.put("brand", "Ergonom");
bike1.put("type", "Enduro bikes");
bike1.put("price", "4972");

Mono<Long> setGetAll = reactiveCommands.hset("bike:1", bike1).doOnNext(result -> {
    System.out.println(result); // >>> 4
});

setGetAll.block();

Mono<String> getModel = reactiveCommands.hget("bike:1", "model").doOnNext(result -> {
    System.out.println(result); // >>> Deimos
});

Mono<String> getPrice = reactiveCommands.hget("bike:1", "price").doOnNext(result -> {
    System.out.println(result); // >>> 4972
});

Mono<List<KeyValue<String, String>>> getAll = reactiveCommands.hgetall("bike:1").collectList().doOnNext(result -> {
    System.out.println(result);
    // >>> [KeyValue[type, Enduro bikes], KeyValue[brand, Ergonom],
    // KeyValue[price, 4972], KeyValue[model, Deimos]]
});
```

```go
hashFields := []string{
    "model", "Deimos",
    "brand", "Ergonom",
    "type", "Enduro bikes",
    "price", "4972",
}

res1, err := rdb.HSet(ctx, "bike:1", hashFields).Result()

if err != nil {
    panic(err)
}

fmt.Println(res1) // >>> 4

res2, err := rdb.HGet(ctx, "bike:1", "model").Result()

if err != nil {
    panic(err)
}

fmt.Println(res2) // >>> Deimos

res3, err := rdb.HGet(ctx, "bike:1", "price").Result()

if err != nil {
    panic(err)
}

fmt.Println(res3) // >>> 4972

cmdReturn := rdb.HGetAll(ctx, "bike:1")
res4, err := cmdReturn.Result()

if err != nil {
    panic(err)
}

fmt.Println(res4)
// >>> map[brand:Ergonom model:Deimos price:4972 type:Enduro bikes]

type BikeInfo struct {
    Model string \`redis:"model"\`
    Brand string \`redis:"brand"\`
    Type  string \`redis:"type"\`
    Price int    \`redis:"price"\`
}

var res4a BikeInfo

if err := cmdReturn.Scan(&res4a); err != nil {
    panic(err)
}

fmt.Printf("Model: %v, Brand: %v, Type: %v, Price: $%v\n",
    res4a.Model, res4a.Brand, res4a.Type, res4a.Price)
// >>> Model: Deimos, Brand: Ergonom, Type: Enduro bikes, Price: $4972
```

```c#
db.HashSet("bike:1", [
    new("model", "Deimos"),
    new("brand", "Ergonom"),
    new("type", "Enduro bikes"),
    new("price", 4972)
]);

Console.WriteLine("Hash Created");
// Hash Created

var model = db.HashGet("bike:1", "model");
Console.WriteLine($"Model: {model}");
// Model: Deimos

var price = db.HashGet("bike:1", "price");
Console.WriteLine($"Price: {price}");
// Price: 4972

var bike = db.HashGetAll("bike:1");
Console.WriteLine("bike:1");
Console.WriteLine(string.Join("\n", bike.Select(b => $"{b.Name}: {b.Value}")));
// Bike:1:
// model: Deimos
// brand: Ergonom
// type: Enduro bikes
// price: 4972
```

```php
$res1 = $r->hmset('bike:1', [
    'model' => 'Deimos',
    'brand' => 'Ergonom',
    'type' => 'Enduro bikes',
    'price' => 4972,
]);

echo $res1 . PHP_EOL;
// >>> 4

$res2 = $r->hget('bike:1', 'model');
echo $res2 . PHP_EOL;
// >>> Deimos

$res3 = $r->hget('bike:1', 'price');
echo $res3 . PHP_EOL;
// >>> 4972

$res4 = $r->hgetall('bike:1');
echo json_encode($res3) . PHP_EOL;
// >>> {"name":"Deimos","brand":"Ergonom","type":"Enduro bikes","price":"4972"}
```

```ruby
res1 = r.hset('bike:1', {
  'model' => 'Deimos',
  'brand' => 'Ergonom',
  'type' => 'Enduro bikes',
  'price' => 4972
})
puts res1 # 4

res2 = r.hget('bike:1', 'model')
puts res2 # Deimos

res3 = r.hget('bike:1', 'price')
puts res3 # 4972

res4 = r.hgetall('bike:1')
puts res4.inspect
# {"model"=>"Deimos", "brand"=>"Ergonom", "type"=>"Enduro bikes", "price"=>"4972"}
```

```rust
let hash_fields = [
    ("model", "Deimos"),
    ("brand", "Ergonom"),
    ("type", "Enduro bikes"),
    ("price", "4972"),
];

if let Ok(res) = r.hset_multiple("bike:1", &hash_fields) {
    let res: String = res;
    println!("{res}");    // >>> OK
}

match r.hget("bike:1", "model") {
    Ok(res) => {
        let res: String = res;
        println!("{res}");   // >>> Deimos
    },
    Err(e) => {
        println!("Error getting bike:1 model: {e}");
        return;
    }
};

match r.hget("bike:1", "price") {
    Ok(res) => {
        let res: String = res;
        println!("{res}");   // >>> 4972
    },
    Err(e) => {
        println!("Error getting bike:1 price: {e}");
        return;
    }
};

match r.hgetall("bike:1") {
    Ok(res) => {
        let res: Vec<(String, String)> = res;
        println!("{res:?}");
        // >>> [("model", "Deimos"), ("brand", "Ergonom"), ("type", "Enduro bikes"), ("price", "4972")]
    },
    Err(e) => {
        println!("Error getting bike:1: {e}");
        return;
    }
};
```

```rust
let hash_fields = [
    ("model", "Deimos"),
    ("brand", "Ergonom"),
    ("type", "Enduro bikes"),
    ("price", "4972"),
];

if let Ok(res) = r.hset_multiple("bike:1", &hash_fields).await {
    let res: String = res;
    println!("{res}");    // >>> OK
}

match r.hget("bike:1", "model").await {
    Ok(res) => {
        let res: String = res;
        println!("{res}");   // >>> Deimos
    },
    Err(e) => {
        println!("Error getting bike:1 model: {e}");
        return;
    }
};

match r.hget("bike:1", "price").await {
    Ok(res) => {
        let res: String = res;
        println!("{res}");   // >>> 4972
    },
    Err(e) => {
        println!("Error getting bike:1 price: {e}");
        return;
    }
};

match r.hgetall("bike:1").await {
    Ok(res) => {
        let res: Vec<(String, String)> = res;
        println!("{res:?}");
        // >>> [("model", "Deimos"), ("brand", "Ergonom"), ("type", "Enduro bikes"), ("price", "4972")]
    },
    Err(e) => {
        println!("Error getting bike:1: {e}");
        return;
    }
};
```

You can get a complete overview of available data types in this documentation site's [data types section](https://redis.io/docs/latest/develop/data-types/). Each data type has commands allowing you to manipulate or retrieve data. The [commands reference](https://redis.io/docs/latest/commands/) provides a sophisticated explanation.

## Scan the keyspace

Each item within Redis has a unique key. All items live within the Redis [keyspace](https://redis.io/docs/latest/develop/using-commands/keyspace/). You can scan the Redis keyspace via the [SCAN command](https://redis.io/docs/latest/commands/scan/). Here is an example that scans for the first 100 keys that have the prefix `bike:`:

```
SCAN 0 MATCH "bike:*" COUNT 100
```

[SCAN](https://redis.io/docs/latest/commands/scan/) returns a cursor position, allowing you to scan iteratively for the next batch of keys until you reach the cursor value 0.

## Next steps

You can address more use cases with Redis by reading these additional quick start guides:

- [Redis as a document database](https://redis.io/docs/latest/develop/get-started/document-database/)
- [Redis as a vector database](https://redis.io/docs/latest/develop/get-started/vector-database/)

## Continue learning with Redis University

See the [Get Started with Redis learning path](https://university.redis.io/learningpath/14q8m6gilfwltm) for courses.