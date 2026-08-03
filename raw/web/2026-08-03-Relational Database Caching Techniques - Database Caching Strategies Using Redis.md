---
title: "Relational Database Caching Techniques - Database Caching Strategies Using Redis"
description: "Many of the caching techniques that are described in this section can be applied to any type of database. However, this paper focuses on relational databases because they are the most common database caching use case."
source_url: "https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/relational-database-caching-techniques.html"
source_domain: "amazon.com"
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
# Relational Database Caching Techniques - Database Caching Strategies Using Redis

> Source: [Relational Database Caching Techniques - Database Caching Strategies Using Redis](https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/relational-database-caching-techniques.html)
> Clipped: 2026-08-03

Relational Database Caching Techniques - Database Caching Strategies Using Redis

This whitepaper is for historical reference only. Some content might be outdated and some links might not be available.

This whitepaper is for historical reference only. Some content might be outdated and some links might not be available.

Many of the caching techniques that are described in this section can be applied to any type of database. However, this paper focuses on relational databases because they are the most common database caching use case.

The basic paradigm when you query data from a relational database includes executing SQL statements and iterating over the returned **ResultSet** object cursor to retrieve the database rows. There are several techniques you can apply when you want to cache the returned data. However, it’s best to choose a method that simplifies your data access pattern and/or optimizes the architectural goals that you have for your application.

To visualize this, this whitepaper will examine snippets of Python code to explain the logic. You can find additional information on the [AWS caching site](https://aws.amazon.com/caching/). The examples use the [redis-py](https://github.com/andymccurdy/redis-py) Redis client library for connecting to Redis, although you can use any other Python Redis library.

Assume that you issued the following SQL statement against a customer database for `CUSTOMER_ID 1001`. This whitepaper will examine the various caching strategies that you can use.

```sql
SELECT FIRST_NAME, LAST_NAME, EMAIL, CITY, STATE, ADDRESS,
COUNTRY FROM CUSTOMERS WHERE CUSTOMER_ID = “1001”;
```

The query returns this record:

Python example:

```php
try:
    cursor.execute(key)
    results = cursor.fetchall()

    for row in results:
        print (row[“FirstName”])
        print (row[“LastName”])
```

Java example:

```cs
…
Statement stmt = connection.createStatement(); 
ResultSet rs = stmt.executeQuery(query);
while (rs.next()) {
Customer customer = new Customer();
customer.setFirstName(rs.getString("FIRST_NAME"));
customer.setLastName(rs.getString("LAST_NAME"));
and so on …
}
…
```

Iterating over the **ResultSet** cursor lets you retrieve the fields and values from the database rows. From that point, the application can choose where and how to use that data.

Assuming that your application framework can’t be used to abstract your caching implementation, how do you best cache the returned database data?

Given this scenario, you have many options. The following sections evaluate some options, with focus on the caching logic.

## Cache the Database SQL ResultSet

Cache a serialized **ResultSet** object that contains the fetched database row.

- **Advantage:** When data retrieval logic is abstracted (for example, as in a [Data Access Object](http://www.oracle.com/technetwork/java/dataaccessobject-138824.html) or DAO layer), the consuming code expects only a **ResultSet** object and does not need to be made aware of its origination. A **ResultSet** object can be iterated over, regardless of whether it originated from the database or was deserialized from the cache, which greatly reduces integration logic. This pattern can be applied to any relational database.
- **Disadvantage:** Data retrieval still requires extracting values from the **ResultSet** object cursor and does not further simplify data access; it only reduces data retrieval latency.

**Note:** When you cache the row, it’s important that it’s serializable. The following example uses a **CachedRowSet** implementation for this purpose. When you are using Redis, this is stored as a byte array value.

The following code converts the **CachedRowSet** object into a byte array and then stores that byte array as a Redis byte array value. The actual SQL statement is stored as the key and converted into bytes.

Python example:

```
if not r.exists(pickle.dumps(key)):

   try:
       cursor.execute(key)
       results = cursor.fetchall()
       r.set(pickle.dumps(key), pickle.dumps(results))
       r.expire(pickle.dumps(key), ttl)
       data = results

   except:
       print (“Error: unable to fetch data.”)
else:
   data = pickle.loads(r.get(pickle.dumps(key)))
```

Java example:

```cs
…
// rs contains the ResultSet, key contains the SQL statement       
   if (rs != null) { //lets write-through to the cache
      CachedRowSet cachedRowSet = new CachedRowSetImpl();    
      cachedRowSet.populate(rs, 1);
      ByteArrayOutputStream bos = new ByteArrayOutputStream(); 
      ObjectOutput out = new ObjectOutputStream(bos); 
      out.writeObject(cachedRowSet);
      byte[] redisRSValue = bos.toByteArray();
      jedis.set(key.getBytes(), redisRSValue);
      jedis.expire(key.getBytes(), ttl);

   }
…
```

One advantage of storing the SQL statement as the key is that it enables a transparent caching abstraction layer that hides the implementation details. The other added benefit is that you don’t need to create any additional mappings between a custom key ID and the executed SQL statement.

At the time of setting data in the Redis, you are applying the expiry time, which is specified in milliseconds.

For lazy caching/cache aside, you would initially query the cache before executing the query against the database. To hide the implementation details, use the DAO pattern and expose a generic method for your application to retrieve the data.

For example, because your key is the actual SQL statement, your method signature could look like the following:

Python example:

```sql
getResultSet(sql) # sql is the sql statement
```

Java example:

```cpp
public  ResultSet getResultSet(String key);    // key is sql 
statement
```

The code that calls (consumes) this method expects only a **ResultSet** object, regardless of what the underlying implementation details are for the interface. Under the hood, the **getResultSet** method executes a GET command for the SQL key, which, if present, is deserialized and converted into a **ResultSet** object.

Python example:

```
def getResultSet(key):

    redisResultSet = None
    redisResultSet = r.get(pickle.dumps(key))

    if redisResultSet:
        rs = pickle.loads(redisResultSet)

    else:
        try:
            cursor.execute(key)
            results = cursor.fetchall()
            r.set(pickle.dumps(key), pickle.dumps(results))
            r.expire(pickle.dumps(key), ttl)
            rs = results

        except:
            print (“Error: unable to fetch data”)

    return rs
```

Java example:

```cs
public ResultSet getResultSet(String key) {
   byte[] redisResultSet = null;
   redisResultSet = jedis.get(key.getBytes()); 
   ResultSet rs = null;
   if (redisResultSet != null) { // if cached value exists, deserialize it and return it
      try {
         cachedRowSet = new CachedRowSetImpl();      
         ByteArrayInputStream bis = new
ByteArrayInputStream(redisResultSet);
         ObjectInput in = new ObjectInputStream(bis);
         cachedRowSet.populate((CachedRowSet) in.readObject()); rs = 
         cachedRowSet;
         } …
} else {
// get the ResultSet from the database, store it in the rs object, then cache it.
…
}
…
return rs;
}
```

If the data is not present in the cache, query the database for it, and cache it before returning.

As mentioned earlier, a best practice would be to apply an appropriate TTL on the keys as well.

For all other caching techniques that we’ll review, you should establish a naming convention for your Redis keys. A good naming convention is one that is easily predictable to applications and developers. A hierarchical structure separated by colons is a common naming convention for keys, such as **object:type:id**.

## Cache Select Fields and Values in a Custom Format

Cache a subset of a fetched database row into a custom structure that can be consumed by your applications.

- **Advantage:** This approach is easy to implement. You essentially store specific retrieved fields and values into a structure such as JSON or XML and then SET that structure into a Redis string. The format you choose should be something that conforms to your application’s data access pattern.
- **Disadvantage:** Your application is using different types of objects when querying for particular data (for example, Redis string and database results). In addition, you are required to parse through the entire structure to retrieve the individual attributes associated with it.

The following code stores specific customer attributes in a customer JSON object and caches that JSON object into a Redis string:

Python example:

```python
try:
    cursor.execute(query)
    results = cursor.fetchall()

    for row in results:
        customer = {
            “FirstName”: row[“FirstName”],
            “LastName”: row[“LastName”]
        }
        r.set(“customer:id:” + str(row[“id”]), json.dumps(customer))

except:
    print (“Error: Unable to fetch data.”)
```

Java example:

```php
…
// rs contains the ResultSet 
while (rs.next()) {
    Customer customer = new Customer(); 
    Gson gson = new Gson();
    JsonObject customerJSON = new JsonObject(); 
    customer.setFirstName(rs.getString("FIRST_NAME")); 
    customerJSON.add(“first_name”,
gson.toJsonTree(customer.getFirstName() ); 
    customer.setLastName(rs.getString("LAST_NAME"));
    customerJSON.add(“last_name”, gson.toJsonTree(customer.getLastName()
);
    and so on …
    jedis.set(customer:id:"+customer.getCustomerID(), customerJSON.toString() );
    }
…
```

For data retrieval, you can implement a generic method through an interface that accepts a customer key (for example, customer:id:1001) and an SQL statement string argument. It will also return whatever structure your application requires (for example, JSON or XML) and abstract the underlying details.

Upon initial request, the application executes a GET command on the customer key and, if the value is present, returns it and completes the call. If the value is not present, it queries the database for the record, writes-through a JSON representation of the data to the cache, and returns.

## Cache Select Fields and Values into an Aggregate Redis Data Structure

Cache the fetched database row into a specific data structure that can simplify the application’s data access.

- **Advantage:** When converting the **ResultSet** object into a format that simplifies access, such as a Redis Hash, your application is able to use that data more effectively. This technique simplifies your data access pattern by reducing the need to iterate over a **ResultSet** object or by parsing a structure like a JSON object stored in a string. In addition, working with aggregate data structures, such as Redis Lists, Sets, and Hashes provides various attribute level commands associated with setting and getting data, and eliminating the overhead associated with processing the data before being able to leverage it.
- **Disadvantage:** Your application is using different types of objects when querying for particular data (for example, Redis Hash and database results).

The following code creates a **HashMap** object that is used to store the customer data. The map is populated with the database data and SET into a Redis.

Python example:

```python
try:
    cursor.execute(query)
    customer = cursor.fetchall()
    r.hset(“customer:id:” + str(customer[“id”]), “FirstName”, customer[0][“FirstName”])
    r.hset(“customer:id:” + str(customer[“id”]), “LastName”, customer[0][“LastName”])

except:
    print (“Error: Unable to fetch data.”)
```

Java example:

```php
…
// rs contains the ResultSet
    while (rs.next()) {
        Customer customer = new Customer();
        Map<String, String> map = new HashMap<String, String>();
        customer.setFirstName(rs.getString("FIRST_NAME"));     
        map.put("firstName", customer.getFirstName()); 
        customer.setLastName(rs.getString("LAST_NAME")); 
        map.put("lastName", customer.getLastName());
        and so on …
        jedis.hmset(customer:id:"+customer.getCustomerID(), map);
        }
…
```

For data retrieval, you can implement a generic method through an interface that accepts a customer ID (the key) and an SQL statement argument. It returns a **HashMap** to the caller. Just as in the other examples, you can hide the details of where the map is originating from. First, your application can query the cache for the customer data using the customer ID key. If the data is not present, the SQL statement executes and retrieves the data from the database. Upon retrieval, you may also store a hash representation of that customer ID to lazy load.

Unlike JSON, the added benefit of storing your data as a hash in Redis is that you can query for individual attributes within it. Say that for a given request you only want to respond with specific attributes associated with the customer Hash, such as the customer name and address. This flexibility is supported in Redis, along with various other features, such as adding and deleting individual attributes in a map.

## Cache Serialized Application Object Entities

Cache a subset of a fetched database row into a custom structure that can be consumed by your applications.

- **Pro:** Use application objects in their native application state with simple serializing and deserializing techniques. This can rapidly accelerate application performance by minimizing data transformation logic.
- **Con:** Advanced application development use case.

The following code converts the customer object into a byte array and then stores that value in Redis:

Python example:

```
try:
    cursor.execute(query)
    results = cursor.fetchall()
    r.set(pickle.dumps(key), pickle.dumps(results))
    r.expire(pickle.dumps(key), ttl)

except:
    print (“Error: Unable to fetch data.”)

#pickle.loads(r.get(pickle.dumps(key)))
```

Java example:

```cs
….
// key contains customer id
    Customer customer = (Customer) object;
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    ObjectOutput out = null;

    try {
        out = new ObjectOutputStream(bos);     
        out.writeObject(customer); out.flush();
        byte[] objectValue = bos.toByteArray();
        jedis.set(key.getBytes(), objectValue);
        jedis.expire(key.getBytes(), ttl);

    }
…
```

The key identifier is also stored as a byte representation and can be represented in the customer:id:1001 format.

As the other examples show, you can create a generic method through an application interface that hides the underlying details method details. In this example, when instantiating an object or hydrating one with state, the method accepts the customer ID (the key) and either returns a customer object from the cache or constructs one after querying the backend database. First, your application queries the cache for the serialized customer object using the customer ID. If the data is not present, the SQL statement executes and the application consumes the data, hydrates the customer entity object, and then lazy loads the serialized representation of it in the cache.

Python example:

```python
def getObject(key):
    
    customer = None
    customer = r.get(key)

    if customer:
        customer = pickle.loads(customer)

    else:
        objectData = key.split(“:”)

        try:
            query = “SELECT * FROM customers WHERE id = ‘%d’ LIMIT 1” % (int(objectData[2]))
            cursor.execute(query)
            results = cursor.fetchall()
            r.set(key, pickle.dumps(results))
            r.expire(key, ttl)
            customer = results

        except:
            print (“Error: Unable to fetch data.”)
    return customer

#result = getObject(“customer:id:1001”)
```

Java example:

```cs
public Customer getObject(String key) {

    Customer customer = null;
    byte[] redisObject = null;
    redisObject = jedis.get(key.getBytes());

    if (redisObject != null) {

        try {

            ByteArrayInputStream in = new
ByteArrayInputStream(redisObject);
            ObjectInputStream is = new ObjectInputStream(in); 
            customer = (Customer) is.readObject();

        } …
            } …
    return customer;
}
```