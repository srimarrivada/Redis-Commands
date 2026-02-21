# Redis Commands
Redis commands are categorized by the data structure it supports. Various categories include `generic`, `string`, `list`, `sorted_set`, `hash`, `pubsub`, etc.

Use the following command to get all commands about a given category:
```
help @<category>
```
These categories include:
* `@generic`: Displays all general commands to manage Redis data.
* `@string`: Displays string commands used for managing string values in Redis.
* `@list`: Displays list commands to manage list data.
* `@set`: Displays set commands to manage data stored in Redis sets.
* `@sorted_set`: Displays commands to manage data stored in Redis sorted sets.
* `@hash`: Displays commands to manage data stored in Redis hashes.
* `@pubsub`: Displays commands to work with Redis publish-subscribe messaging system.
* `@transactions`: Displays Redis transaction commands to execute Redis queries as a batch.
* `@connection`: Displays connection commands to manage client connections with Redis server.
* `@server`: Displays server commands to manage Redis server.
* `@scripting`: Displays scripting commands to run scripts.
* `@hyperloglog`: Displays commands related to hyperloglog.

For example, use the following command to get all generic commands:
```
help @generic
```

To get help on a specific command, use `help <command>` syntax.  
For example, use the following command to get help on `PING` command:
```
help ping
```
## 1. Key Management Commands
Redis key commands are used to manage keys in the database. All keys in Redis database are case sensitive _(for instance, Redis identifies `NAME` and `name` as two different keys)_.  
Some of these commands include:

* `SET`: It sets the value for the specified key.  
  Follow the below syntax to use this command:
  
  ```
  SET key value
  ```
  For example, use the below command to set `user_id` key to `101`:
  ```
  set user_id 101
  ```

* `GET`: It retrieves the value stored in the specified key.  
  Follow the below syntax to use this command:
  ```
  GET key
  ```
  For example, use the below command to get `user_id` key value:
  ```
  get user_id
  ```
   
* `EXISTS`: It verifies if the given key is present in the database. It returns `1` if the key exists or `0` if the key does not exist.  
  Follow the below syntax to use this command:
  ```
  EXISTS key
  ```

  For example, use the below command to verify the existence of `user_id` key:
  ```
  exists user_id
  ```
  
* `KEYS`: It lists out all keys in the database matching a specific pattern. On larger datasets, this command can freeze the entire server while it scans the key space.  
  Follow the below syntax to use this command:
  ```
  KEYS pattern
  ```
  The key pattern can include:  
  `*` : Matches any number of characters (including none).  
  `?`  : Matches exactly one character.  
  `[abc]` : Matches any character inside the brackets.  
  `[a-z]` : Matches any character in the specified range.  
  
  For example, use the below command to get all keys:
  ```
  keys *
  ```

  Use the below command to get all keys starting with `u`:
  ```
  keys u*
  ```

  Use the below command to get all keys containing `i` string:
  ```
  keys *i*
  ```

* `SCAN`: It is a cursor-based iterator that traverses all keys in the Redis database incrementally. Unlike the `KEYS` command which can block the Redis server for several seconds on large datasets, `SCAN` returns a small batch of keys per call.  
  Follow the below syntax to use this command:
  ```
  SCAN cursor
  ```
  
  This command can take parameters such as:
  * `MATCH pattern` which allows to filter keys based on the given pattern.
  * `COUNT count` which gives a hint about how much work to do for each call (default is `10`). 
  
  It is always good to start the `SCAN` operation with cursor `0` which returns an array of elements with first element being the new cursor and second element with a list of keys scanned. In the next `SCAN` command, use the new cursor returned by the previous `SCAN` and continue scanning until it returns `0` cursor.
  
  For example, use the below command to scan keys:
  ```
  scan 0
  ```
  
* `RENAME`: It renames the specified key.  
  Follow the below syntax to use this command:
  ```
  RENAME old_key new_key
  ```

  For example, use the below command to rename `user_id` key name to `userid`:
  ```
  rename user_id userid
  ```

* `TYPE`: It returns the data type of the value stored in the key.  
  Follow the below syntax to use this command:
  ```
  TYPE key
  ```
  For example, use the below command to return the datatype of `userid` key:
	```
  type userid
  ```

* `EXPIRE`: It sets the timeout in seconds on the specified key after which the key gets automatically deleted from the database by the Redis server.   
  Follow the below syntax to use this command:
  ```
  EXPIRE key time
  ```

  For example, use the below command to set `60` seconds expiry on the `userid` key:
  ```
  expire userid 60
  ```

  To set the expire on the new key, use `EX` option with `SET` command as below:
  ```
  SET key value EX time
  ```

  For example, use the below command to set `360` seconds expiry on the `phone` key:
  ```
  set phone "1-123-4567890" EX 360
  ```
 	
* `TTL`: It returns the remaining time of key before it gets deleted from the Redis database.  TTL is used to decide which keys can be deleted from the server after certain time, keeping the cache size manageable even in the case of caching more items than memory allows.  
  Follow the below syntax to use this command:
  ```
  TTL key
  ```
  
  For example, use the below command to get the available time of `userid` key before it expires:
  ```
  ttl userid
  ```

* `PERSIST`: It removes the expiry set on the key so the key exists in the database until it is deleted manually.  
  Follow the below syntax to use this command:
  ```
  PERSIST key
  ```
  For example, use the below command to remove expiry on the `userid` key:
  ```
  persist userid
  ```

* `DUMP`: It provides the serialized version of the value stored at the specific key. The returned value is in a binary format that can only be understood by Redis and is typically used for data migration or backups.  
  Follow the below syntax to use this command:
  ```
  DUMP key
  ```

  For example, use the below command to get the serialized value of `userid` key:
  ```
  dump userid
  ```

* `RESTORE`: It allows to create a key using the serialized value generated by the `DUMP` command.   
  Follow the below syntax to use this command:
  ```
  RESTORE key ttl serialized-value
  ```
  
  For example, use the below command to create `user_id` key with the above serialized value:
  ```
  restore user_id 0 <serialized value from dump>
  ```

* `DEL`: It deletes the specified key.  
  Follow the below syntax to use this command:
  ```
  DEL key
  ```

  For example, use the below command to delete `userid` key:
  ```
  del userid
  ```

## 2. String Commands
Redis string commands are used to manage values stored at specified key.  
Some of these commands include:

* `SET`: It sets the value for the specified key. 
  Follow the below syntax to use this command:
  ```
  SET key value
  ```

  For example, use the below command to set a `message` key:
  ```
  set message "Hello Redis!"
  ```
 
* `MSET`: It sets values for multiple keys. If a key already exists in the database, the value is overwritten.  
  Follow the below syntax to use this command:
  ```
  MSET key1 value1 key2 value2
  ```

  For example, use the below command to set `name`, `age` and `email` keys:
  ```
  mset name "John Doe" age 30 email "John.Doe@example.com"
  ```

* `GET`: It retrieves the value stored in the specified key.  
  Follow the below syntax to use this command:
  ```
  GET key
  ```
  
  For example, use the below command to get `message` key value:
  ```
  get message
  ```

* `MGET`: It retrieves values from multiple keys.   
  Follow the below syntax to use this command:
  ```
  MGET key1 key2 key3
  ```
  
  For example, use the below command to get `name` and `age` keys value:
  ```
  mget name age
  ```
  
* `APPEND`: It appends the value to the existing key.  
  Follow the below syntax to use this command:
  ```
  APPEND key value
  ```
  
  For example, use the below command to append value to `message` key:
  ```
  append message "Welcome to the world!"
  ```

* `SETRANGE`: It overwrites the part of string stored in a key starting at a specific offset.  It starts at the offset and replaces the existing characters with the new value.  
  Follow the below syntax to use this command:
  ```
  SETRANGE key offset value
  ```
  
  For example, use the below command to overwrite value in the `message` key starting from offset `6`:
  ```
  setrange message 6 "Redis, Welcome to this world!"
  ```

* `GETRANGE`: It returns the substring of a string stored at the key.  
  Follow the below syntax to use this command:
  ```
  GETRANGE key start_offset end_offset
  ```

  For example, use the below command to retrieve substring from offset `6` to `10` of a value at `message` key:
  ```
  getrange message 6 10
  ```

* `STRLEN`: It returns the length of the string stored at the specified key.  
  Follow the below syntax to use this command:
  ```
  STRLEN key
  ```

  For example, use the below command to get the length of value in `message` key:
  ```
  strlen message
  ```

* `INCR`: It increments the integer value of the new or existing key by 1.  
  Follow the below syntax to use this command:
  ```
  INCR key
  ```
  For example, use the below command to increment the `age` key:

  ```
  incr age
  ```
 
* `INCRBY`: It increments the integer value of the new or existing key by the given number.   
  Follow the below syntax to use this command:
  ```
  INCRBY key number
  ```
  
  For example, use the below command to increment the `age` key by `10`:
  ```
  incrby age 10
  ```
 
* `DECR`: It decrements the integer value of the new or existing key by 1.   
  Follow the below syntax to use this command:
  ```
  DECR key
  ```
  For example, use the below command to decrement the `counter` key which does not exist in the database:
  ```
  decr counter
  ```
  It creates a key named `counter` and sets its value to `-1`.

* `DECRBY`: It decrements the integer value of the new or existing key by the given number.  
  Follow the below syntax to use this command:
  ```
  DECRBY key number
  ```
  
  For example, use the below command to decrement the `age` key by `10`:
  ```
  decrby age 10
  ```
 
## 3. List Commands
Redis list commands are used to manage collection of strings sorted by the order of insertion in a list. Elements can be added at the top or bottom of the list.  
Some of these commands include:

* `LPUSH`: It adds one or more values to the beginning of the list.   
  Follow the below syntax to use this command:
  ```
  LPUSH key value1 value2
  ```
  For example, use the below command to add elements to `colors` list at the start:
  ```
  lpush colors Red Blue Green
  ```

* `RPUSH`: It adds one or more values to the end of the list.  
  Follow the below syntax to use this command:
  ```
  RPUSH key value1 value2
  ```
  
  For example, use the below command to add elements to `colors` list at the end:
  ```
  rpush colors Orange
  ```
  
* `LINSERT`: It inserts an element before or after a specific element in a list. 
  Follow the below syntax to use this command:
  ```
  LINSERT key BEFORE|AFTER pivot value
  ```

  For example, use the below command to insert `White` element before `Blue` element in `colors` list:
  ```
  linsert colors before Blue White
  ```

* `LSET`: It overrides an element to the list at a specific index.  
  Follow the below syntax to use this command:
  ```
  LSET key index value
  ```
  
  For example, use the below command to override element at index `2` to `colors` list:
  ```
  lset colors 2 Black
  ```

* `LLEN`: It returns the number of elements in a list.  
  Follow the below syntax to use this command:
  ```
  llen key
  ```

  For example, use the below command to get the length of `colors` list:
  ```
  llen colors
  ```

* `LRANGE`: It retrieves range of elements from the list. Note that the list stores elements starting with index `0`.  
  Follow the below syntax to use this command:
  ```
  lrange key start_index end_index
  ```
  
  For example, use the below command to get first 3 elements in `colors` list:
  ```
  lrange colors 0 2
  ```

  Use the below command to get all elements in `colors` list:
  ```
  lrange colors 0 -1
  ```

* `LPOP`: It removes and returns first element in the list.  
  Follow the below syntax to use this command:
  ```
  LPOP key
  ```
  
  For example, use the below command to remove first element in `colors` list:
  ```
  lpop colors
  ```
  
* `RPOP`: It removes and returns last element in the list.  
  Follow the below syntax to use this command:
  ```
  RPOP key
  ```

  For example, use the below command to remove last element in `colors` list:
  ```
  rpop colors
  ```
 
## 4. Set Commands
Redis set commands are used to manage unordered collection of unique strings in a set.  
Some of these commands include:

* `SADD`: It adds one or more members to a set.  
  Follow the below syntax to use this command:
  ```
  SADD key member1 member2
  ```
  
  For example, use the below command to add members to `countries` set:
  ```
  sadd countries USA Canada India China India
  ```
    You can see that though `India` is specified twice, it added only once and returned with number of members added.
  
* `SCARD`: It returns the number of members in a set.  
  Follow the below syntax to use this command:
  ```
  SCARD key
  ```

  For example, use the below command to get number of elements in `countries` set:
  ```
  scard countries
  ```

* `SISMEMBER`: It checks if the given member exists in the specified set or not. It returns `1` if the member exists or `0` if the member does not exist.   
  Follow the below syntax to use this command:
  ```
  SISMEMBER key member
  ```
  For example, use the below command to verify if `Japan` exists in `countries` set:
  ```
  sismember countries Japan
  ```

* `SMEMEBERS`: It displays all available members in a set. When a set contains huge members, this command can freeze the entire server.  
  Follow the below syntax to use this command:
  ```
  SMEMBERS key
  ```

  For example, use the below command to see all members in `countries` set:
  ```
  smembers countries
  ```

* `SSCAN`: It is a cursor-based iterator that iterates through all members in a Redis set object. Unlike `SMEMBERS`, it returns members in smaller batches using a cursor.  
  Follow the below syntax to use this command:
  ```
  SSCAN key cursor
  ```
  
  This command can take parameters such as:
  * `MATCH pattern` which allows to filter members based on the given pattern.
  * `COUNT count` which gives a hint about how much work to do for each call (default is `10`). 

  It is always good to start the `SSCAN` operation with cursor `0` which returns an array of elements with first element being the new cursor and second element with a list of keys scanned. In the next `SSCAN` command, use the new cursor returned by the previous `SSCAN` and continue scanning until it returns `0` cursor.

  For example, use the below command to scan `countries` set with a match of members ending with `a`:
  ```
  sscan countries 0 match *a
  ```
 
* `SREM`: It removes one or more members from the set.  
  Follow the below syntax to use this command:
  ```
  SREM key member1 member2
  ```

  For example, use the below command to remove `India` and `USA` from `countries` set:
  ```
  srem countries India USA
  ```

* `SUNION`: It adds unique members from two sets and return those members.  
  Follow the below syntax to use this command:
  ```
  sunion key1 key2
  ```
  For example, first create a new set using the below command:
  ```
  sadd countries_new India China Japan USA
  ```

  Then, use the below command to add `countries` and `countries_new` sets:
  ```
  sunion countries countries_new
  ```
  
* `SDIFF`: It returns the members of a set resulting from the difference between the first set and all subsequent sets.  
  Follow the below syntax to use this command:
  ```
  sdiff key1 key2
  ```
  
  For example, use the below command to find the difference of elements in `countries` set compared to `countries_new` set:
  ```
  sdiff countries countries_new
  ```

  Use the below command to find the difference of elements in `countries_new` set compared to `countries` set:
  ```
  sdiff countries_new countries
  ```

* `SINTER`: It returns common members from first and subsequent sets.  
  Follow the below syntax to use this command:
  ```
  sinter key1 key2
  ```

  For example, use the below command to display common members between `countries` and `countries_new` list:
  ```
  sinter countries countries_new
  ```

## 5. Sorted Set Commands
Redis sorted set commands are used to manage unique strings in a set. Sorted Sets (also called **Zsets**) are unique because every member is associated with a score, which Redis uses to keep the set ordered.  
Some of these commands include:

* `ZADD`: It adds one or more members to a sorted set or updates score if the member already exists.  
  Follow the below syntax to use this command:
  ```
  ZADD key score1 member1 score2 member2
  ```
  
  For example, use the below command to add members along with their scores to `languages` sorted set:
  ```
  zadd languages 550 Spanish 600 Hindi 1100 Chineese 1500 English
  ```
  
* `ZCARD`: It returns the number of members in a set.   
  Follow the below syntax to use this command:
  ```
  ZCARD key
  ```
  For example, use the below command to get number of elements in languages sorted set:
  ```
  zcard languages
  ```

* `ZSCORE`: It returns the score associated to a given member in the sorted set.   
  Follow the below syntax to use this command:
  ```
  ZSCORE key member
  ```

  For example, use the below command to get the score of `Hindi` in `languages` sorted set:
  ```
  zscore languages Hindi
  ```

* `ZRANGE`: It retrieves range of members in a sorted set. When a sorted set contains huge members, this command can freeze the entire server.  
  Follow the below syntax to use this command:
  ```
  zrange key start_index end_index
  ```

  For example, use the below command to get first 2 members in `languages` sorted set:
  ```
  zrange languages 0 1
  ```
 
  Use the below command to display members along with scores in `languages` sorted set:
  ```
  zrange languages 0 2 WITHSCORES
  ```

  Use the below command to display all members in `languages` sorted set:
  ```
  zrange languages 0 -1
  ```
 
 * `ZSCAN`: It is a cursor-based iterator that iterates through all members and their associated scores in a Redis sorted set object. Unlike `ZRANGE`, it returns members in smaller batches using a cursor without blocking the server.
   Follow the below syntax to use this command:
   ```
   ZSCAN key cursor
   ```
   This command can take parameters such as
   * `MATCH pattern` which allows to filter members based on the given pattern.
   * `COUNT count` which gives a hint about how much work to do for each call (default is `10`).
   
   It is always good to start the `ZSCAN` operation with cursor `0` which returns an array of elements with first element being the new cursor and second element with a list of keys scanned. In the next `ZSCAN` command, use the new cursor returned by the previous `ZSCAN` and continue scanning until it returns `0` cursor.

   For example, use the below command to scan `languages` sorted set with a match of members ending with `ish`:
   ```
   zscan languages 0 match *ish
   ```
   
* `ZINCRBY`: It increments the score of a member in the sorted set.  
  Follow the below syntax to use this command:
  ```
  ZINCRBY key increment member
  ```
  
  For example, use the below command to increment `Hindi` score by `100` in `languages` sorted set:
  ```
  zincrby languages 100 Hindi
  ```

* `ZCOUNT`: It counts the members that fall under the specified range of scores in a sorted set.   
  Follow the below syntax to use this command:
  ```
  ZCOUNT key min_score max_score
  ```
  For example, use the below command to get the count of members associated with scores ranging from `500` to `1000` in `languages` sorted set:
  ```
  zcount languages 500 1000
  ```
 
* `ZREM`: It removes one or more members from the sorted set.  
  Follow the below syntax to use this command:
  ```
  ZREM key member1 member2
  ```
  For example, use the below command to remove `Hindi` and `English` members from the `languages` sorted set:
  ```
  zrem languages Hindi English
  ```
 
 ## 6. Hash Commands
Redis hashes are maps between string fields and string values, making them suitable to represent objects such as users, products, sessions, etc. Redis hashes stores set of key-value pairs, providing the ability to add, fetch, or remove individual items, fetch the entire hash, or use one or more fields in the hash as a counter.  
Some of these commands include:

* `HSET`: It sets the value of one field in a hash. If the field does not exist, it creates the field and assigns a value to it.  
  Follow the below syntax to use this command:
  ```
  HSET key field value
  ```
  
  For example, use the below command to set the `name` field for `user:101` key:
  ```
  hset user:101 name "John"
  ```

* `HMSET`: It sets the values for multiple fields in a hash.  
  Follow the below syntax to use this command:
  ```
  HMSET key field1 value1 field2 value2
  ```

  For example, use the below command to set the `age` and `email` fields for `user:101` key:
  ```
  hmset user:101 age 30 email "John.Doe@example.com"
  ```

* `HGET`: It retrieves the value of a specific hash field.  
  Follow the below syntax to use this command:
  ```
  HGET key field 
  ```
  For example, use the below command to get the `age` field value of `user:101` key:
  ```
  hget user:101 age
  ```

* `HMGET`: It retrieves the values of multiple fields.  
  Follow the below syntax to use this command:
  ```
  HMGET key field1 field2
  ```
  
  For example, use the below command to get `name`, `age` and `email` fields value of `user:101` key:
  ```
  hmget user:101 name age email
  ```

* `HEXISTS`: It verifies if the given field is present in the specified hash key. It returns `1` if the field exists or `0` if the field does not exist.  
  Follow the below syntax to use this command:
  ```
  HEXISTS key field
  ```
  
  For example, use the below command to verify `name` field in `user:101` hash:
  ```
  hexists user:101 name
  ```

* `HKEYS`: It returns the list of all fields available in a hash.  
  Follow the below syntax to use this command:
  ```
  HKEYS key
  ```

  For example, use the below command to get all fields in `user:101` hash:
  ```
  hkeys user:101
  ```

* `HVALS`: It returns the list of all values available in a hash.   
  Follow the below syntax to use this command:
  ```
  HVALS key
  ```
  For example, use the below command to get all values in `user:101` hash:
  ```
  hvals user:101
  ```

* `HGETALL`: It returns the list of all fields and values available in a hash. When a hash contains millions of fields, this command can freeze the entire server.  
  Follow the below syntax to use this command:
  ```
  HGETALL key
  ```
  
  For example, use the below command to get all fields and values in `user:101` hash:
  ```
  hgetall user:101
  ```

* `HSCAN`: It is a cursor-based iterator that iterates through all fields in a Redis hash. Unlike `HGETALL`, it returns fields in smaller batches using a cursor.  
  Follow the below syntax to use this command:
  ```
  HSCAN key cursor
  ```
  
  This command can take parameters such as
  * `MATCH pattern` which allows to filter fields based on the given pattern.
  * `COUNT count` which gives a hint about how much work to do for each call (default is `10`). 
  
  It is always good to start the `HSCAN` operation with cursor `0` which returns an array of elements with first element being the new cursor and second element with a list of keys scanned. In the next `HSCAN` command, use the new cursor returned by the previous `HSCAN` and continue scanning until it returns `0` cursor.
  
  For example, use the below command to scan `user:101` hash with a match of field `name` beginning with `n` and a hint of `2`:
  ```
  hscan user:101 0 match n* count 2
  ```

* `HLEN`: It returns the number of fields in a hash.   
  Follow the below syntax to use this command:
  ```
  HLEN key
  ```
  For example, use the below command to get the length of `user:101` key:
  ```
  hlen user:101
  ```

* `HINCRBY`: It increments the integer value of the hash field by the given number.  
  Follow the below syntax to use this command:
  ```
  HINCRBY key field increment
  ```
  For example, use the below command to increment the `age` field by `5`:
  ```
  hincrby user:101 age 5
  ```

* `HDEL`: It deletes one or more fields in a hash.  
  Follow the below syntax to use this command:
  ```
  HDEL key field1 field2
  ```

  For example, use the below command to delete `name` and `age` fields in `user:101` key:
  ```
  hdel user:101 name age
  ```

## 7. HyperLogLog Commands
Redis HyperLogLog (HLL) is an algorithm that estimates the number of unique elements in a set using only a tiny amount of memory (max **12 KB** per key). It is designed for high-efficiency cardinality estimation up to **264** items per set with **99%** accuracy (standard error of **0.81%**). HLL is particularly for tracking unique events in massive datasets, such as monitoring network traffic, calculating unique website visitors, or analyzing high-volume data streams where exact counting is computationally expensive.  It solves the "unique count" problem at scale without crashing the server's RAM.  

Redis provides three commands to manage HyperLogLogs.  
These commands include:
* `PFADD`: It adds one or more members to the specified HyperLogLog and returns `1` _(if the internal estimate was updated)_ or `0` _(if the element was already counted)_.  
  Follow the below syntax to use this command:
  ```
  PFADD key element 1 element2
  ```
  
  For example, use the below command to add 3 users to the `user_login` set:
  ```
  pfadd user_login user1 user2 user3
  ```

* `PFCOUNT`: It returns the approximate number of unique items in one or more sets observed by the HyperLogLog at keys.  
  Follow the below syntax to use this command:
  ```
  PFCOUNT key1 key2
  ```
  For example, use the below command to get the cardinality of `user_login` set:
  ```
  pfcount user_login
  ```

* `PFMERGE`: It merges multiple HyperLogLogs into a single destination key. This is helpful for aggregating daily unique visitor counts into a weekly or monthly total.   
  Follow the below syntax to use this command:
  ```
  PFMERGE destinationkey sourcekey1 sourcekey2
  ```

## 8. PubSub Commands
Redis Pub/Sub implements a messaging system where it allows senders (publishers) to publish messages to channels and multiple consumers (subscribers) listening to those channels receive messages in real time.
Redis pub/sub provides core built-in commands to manage pub-sub data.  
Some of these commands include:

* `PUBLISH`: It publishes a message to the Redis channel. Once the message is published, it returns with number of clients that message is sent to _(if it returns `0`, it means the message is not published to any client)_.   
  Follow the below syntax to use this command:
  ```
  PUBLISH channel message
  ```

  For example, use the below command to publish message to `redis_chat` channel:
  ```
  publish redis_chat "Hello Redis!"
  ```
  It returns with `0` indicating that the message is not published to any clients since no one has subscribed to `redis_chat` channel yet.

* `SUBSCRIBE`: It listens for messages published to one or more Redis channels. Once the subscription is successful, it displays three events – the first event is the `subscribe` string which confirms the subscription, second event is the channel name, third event shows the number of clients it has subscribed so far.   
  Follow the below syntax to use this command:
  ```
  SUBSCRIBE channel1 channel2
  ```

  For example, open a new command prompt and run the following command to subscribe to multiple channels:
  ```
  redis-cli subscribe redis_chat news:sports news:weather news:tech
  ```
 
  You can see that it displays multiple events such as `subscribe` string, respective channel name and number of channels it has subscribed.
  
  Now go to another command prompt where `redis-cli` is launched and run the following commands to publish messages to various channels:
  ```
  publish redis_chat "Hello, Redis!"
  publish news:sports "Ronaldo scores!"
  publish news:tech "New Redis version released!"
  publish news:weather "Today seems very cold!"
  ```

  Now, go to command prompt where channels are subscribed and you can see messages it has received. It displays three events for each message it has received – the first event is the `message` string indicating the message event, second event is the channel name, third event shows the actual message.  
  
  Press **Ctrl+C** to exit from subscription window.

* `UNSUBSCRIBE`: It unsubscribes from the specified channels. Once the unsubscription is successful, it displays three events – the first event is the `unsubscribe` string which confirms the unsubscription, second event is the channel name, third event returns `0` indicating that it has unsubscribed. 

  Follow the below syntax to use this command:
  ```
  UNSUBSCRIBE channel
  ```
  For example, use the below command to unsubscribe from `news:sports` channel:
  ```
  unsubscribe news:sports
  ```

  It displays multiple events such as `unsubscribe` string, respective channel name and `0`.

* `PSUBSCRIBE`: It allows to subscribe to multiple channels matching the pattern specified. This is useful when there are dozens of channels (for example, one for ever user) and subscribe to them all with one single command. Once the subscription is successful, it displays three events – the first event is the `psubscribe` string which confirms the pattern subscription, second event is the channel name with pattern, third event shows the number of clients it has subscribed so far.  
  Follow the below syntax to use this command:
  ```
  PSUBSCRIBE channel
  ```
  The pattern can include:  
  `*` : Matches any number of characters (including none).  
  `?` : Matches exactly one character.  
 `[abc]` : Matches any character inside the brackets.  
 `[a-z]` : Matches any character in the specified range.  

  For example, open a new command prompt and run the following command to subscribe to multiple `orders` channels:
  ```
  redis-cli psubscribe orders:*
  ```

  You can see that it displays multiple events such as `psubscribe` string, respective channel name with pattern and number of channels it has subscribed.

  Now go to another command prompt where `redis-cli` is launched and run the following commands to publish messages to various channels:
  ```
  publish orders:101 "Paid"
  publish orders:105 "Delivered"
  publish orders:102 "Shipped"
  publish orders:850 "Ordered"
  ````

  Now, go to command prompt where channels are subscribed and you can see messages it has received. It displays three events for each message it has received – the first event is the `pmessage` string indicating the pattern message event, second event is the channel name with pattern, third event shows the actual message.

  Press **Ctrl+C** to exit from subscription window.

* `PUNSUBSCRIBE`: It allows to unsubscribe from multiple channels matching the pattern specified.   
  Follow the below syntax to use this command:
  ```
  PUNSUBSCRIBE channel
  ```
  The pattern can include:  
  `*` : Matches any number of characters (including none).  
  `?` : Matches exactly one character.  
  `[abc]` : Matches any character inside the brackets.  
  `[a-z]` : Matches any character in the specified range.  

  For example, use the below command to unsubscribe from all channels starting with chat:
  ```
  punsubscribe orders:*
  ```

* `PUSUB CHANNELS`: It returns the list of all currently active channels.  
  Follow the below syntax to use this command:
  ```
  pubsub channels
  ```

  For example, use the below command to get all active channels:
  ```
  pubsub channels
  ```
  Currently, it displays empty list as no Redis channels have not subscribed yet to the current Redis instance. 

  Now, open a new command prompt and run the following command to subscribe to `news:sports` and `news:tech` channels:
  ```
  redis-cli subscribe news:sports news:tech
  ```

  Now go to another command prompt where `redis-cli` is launched and run the following commands to verify active channels:
  ```
  pubsub channels
  ```

* `PUBSUB NUMPAT`: It returns the total number of pattern subscriptions.  
  Follow the below syntax to use this command:
  ```
  PUBSUB NUMPAT
  ```

  For example, use the below command to get total pattern subscriptions:
  ```
  pubsub numpat
  ```
  Currently, it displays empty list as no Redis channels have not subscribed yet to the current Redis instance. 

  Now, open a new command prompt and run the following command to subscribe to `news:sports` and `news:tech` channels:
  ```
  redis-cli psubscribe orders:*
  ```
 
  Now go to another command prompt where `redis-cli` is launched and run the following commands to verify active pattern subscriptions:
  ```
  pubsub numpat
  ```

* `PUBSUB NUMSUB`: It returns the number of subscribers for the specified channels.  
  Follow the below syntax to use this command:
  ```
  PUBSUB NUMSUB channel
  ```
  For example, use the below command to get the number of subscribers for `news:sports` channel:
  ```
  pubsub numsub news:sports
  ```
 
## 9. Transaction Commands
Redis transaction commands allow to execute group of commands in a single step, so they are executed sequentially as a single isolated operation. Redis will not serve any other client requests during this transaction. Redis transaction is atomic (either all commands or none will be executed).  
These commands include:
* `MULTI`: It marks the start of a transaction block so that the subsequence commands will be queued instead of executed immediately.  
  Follow the below syntax to use this command:
  ```
  MULTI
  ```
  
* `EXEC`: It executes all commands that were queued after `MULTI` command.  
  Follow the below syntax to use this command:
  ```
  EXEC
  ```
  
* `DISCARD`: It flushes all queued commands and exits from the transaction state. All subsequent commands after this will be executed immediately.  
  Follow the below syntax to use this command:
  ```
  DISCARD
  ```
  
* `WATCH`: It monitors one or more keys and if any of those keys fail before running `EXEC` command, the transaction will fail. It provides a kind of optimistic locking.   
  Follow the below syntax to use this command:
  ```
  WATCH key
  ```

* `UNWATCH`: It flushes all previously watched keys.  
  Follow the below syntax to use this command:
  ```
  UNWATCH
  ```

  For example, run the following commands to execute a Redis transaction.
  ```
  watch user1:email user2:email
  multi
  set user1:name "Steven King"
  set user1:age 40
  set user1:email sking@email.com
  incr usercount
  set user2:name "John Chen"
  set user2:age 35
  set user2:email johnchen@email.com
  incr usercount
  exec
  unwatch
  ```

## 10. Server Commands
Redis provides various commands to perform administration activities such as managing Redis server instance, inspecting performance, controlling persistence, etc.  
Some of these commands include:
* `INFO`: It provides the massive dump of server stats like memory usage, connected clients, persistence, CPU, replication and many more.  
  Follow the below syntax to use this command:
  ```
  INFO
  ```
  To get information about specific section such as memory, clients, specify the section name next to info.
  
  For example, run the below command to get details about memory:
  ```
  info memory
  ```

  Run the below command to get details about persistence:
  ```
  info persistence
  ```

* `COMMAND`: It provides the massive array of commands with name, number of arguments, flags and key-positioning details for every command supported by Redis server.  
  Follow the below syntax to use this command:
  ```
  COMMAND
  ```
 
 * `COMMAND INFO`: It provides the details of one or more Redis commands.
   Follow the below syntax to use this command:
   ```
   COMMAND INFO command
   ```

   For example, run the below command to get details about `scan` and `flushall` commands:
   ```
   command info scan flushall
   ```
   
* `COMMAND COUNT`: It provides the total number of commands supported by the current Redis version.  
  Follow the below syntax to use this command:
  ```
  COMMAND COUNT
  ```

* `CLIENT LIST`: It displays the information and statistics including IP address, connection age, idle time, current database, and last command executed of all connected clients.  
  Follow the below syntax to use this command:
  ```
  CLIENT LIST
  ```

* `CLIENT SETNAME`: It allows to assign a human-readable name to the current connection for easier identification in `CLIENT LIST`.   
  Follow the below syntax to use this command:
  ```
  CLIENT SETNAME name
  ```

  For example, use the below command to set current connection name as `redis_local`.
  ```
  CLIENT SETNAME redis_local
  ```

* `CLIENT GETNAME`: It retrieves the name set for the current connection.  
  Follow the below syntax to use this command:
  ```
  CLIENT GETNAME
  ```

* `TIME`: It displays the current server time in a specific two element format. It returns an array with two integers – first integer indicates a Unix Timestamp with seconds elapsed since **January 1, 1970** and second integer indicates the remaining microseconds in the current second.  
  Follow the below syntax to use this command:
  ```
  TIME
  ```

* `PING`: It tests if the connection to the Redis server is alive. If no arguments are passed, it returns with `PONG` string. If a message is passed as an argument, the server returns that exact message back.  
  Follow the below syntax to use this command:
  ```
  PING
  ```

  Run the following command to ping Redis server with a message:
  ```
  PING "Hello Redis"
  ```
 
* `DBSIZE`: It returns the total number of keys stored in the Redis database.  
  Follow the below syntax to use this command:
  ```
  DBSIZE
  ```

* `CONFIG GET`: It reads the current configuration parameters of the Redis server from the `redis.conf` file.  
  Follow the below syntax to use this command:
  ```
  CONFIG GET config 
  ```
  For example, use the below command to get all configuration values:
  ```
  CONFIG GET *
  ```
 
  Use the below command to get the current `maxmemory` configuration:
  ```
  CONFIG GET maxmemory
  ```

  Use the below command to get the Redis installation directory:
  ```
  CONFIG GET dir
  ```

* `CONFIG SET`: It updates the configuration on the Redis server without needing to restart.  
  Follow the below syntax to use this command:
  ```
  CONFIG SET parameter value
  ```

  For example, use the below command to set `maxmemory` to `2gb`:
  ```
  config set maxmemory 2gb
  ```

* `SAVE`: It synchronously saves the dataset to disk, producing a point-in-time snapshot (RDB file) of all data in the Redis instance. It blocks all client connections and will not respond to any other commands until the snapshot is complete.  
  Follow the below syntax to use this command:
  ```
  SAVE
  ```

  To verify the saved file, check the configuration for the directory and filename (`dump.rdb`) using the following commands:
  ```
  config get dir
  config get dbfilename
  ```

* `BGSAVE`: It manually triggers a background save of the dataset to disk, producing a point-in-time snapshot (RDB file) of all data in the Redis instance without blocking any client connections. It instructs Redis to fork a child process to write the RDB file to disk while the main process continues to serve client requests.   
  Follow the below syntax to use this command:
  ```
  BGSAVE
  ```
 
  To verify the saved file, check the configuration for the directory and filename (`dump.rdb`) using the following commands:
  ```
  config get dir
  config get dbfilename
  ```

* `LASTSAVE`: It returns the UNIX timestamp (number of seconds since **January 1, 1970**) of last successful save to disk. It is primarily used to verify if the background save (`BGSAVE`) has finished.  
  Follow the below syntax to use this command:
  ```
  LASTSAVE
  ```
 
 * `FLUSHDB`: It synchronously deletes all keys in the currently selected database, blocking all client connections until the deletion is complete. To perform asynchronous deletion, use `ASYNC` flag so that server do not freeze. Once this command is executed, it cannot be reverted unless there is a recent RDB or AOF backup.  
   Follow the below syntax to use this command:
   ```
   FLUSHDB
   ```
   
* `FLUSHALL`: It deletes all keys from all existing databases in the Redis instance. It clears the RDB persistence file and aborts any snapshots in progress. This command is useful when complete server reset is needed.  
  Follow the below syntax to use this command:
  ```
  FLUSHALL
  ```
 
* `SHUTDOWN`: It synchronously saves data and gracefully stops the Redis server by terminating client connections and ensuring data is safely persisted to disk before exiting.   
  Follow the below syntax to use this command:
  ```
  SHUTDOWN
  ```

  This command accepts various flags to modify the shutdown behavior:
  * `SHUTDOWN SAVE`: It forces to perform a final snapshot even if no save points are configured in the configuration file.
  * `SHUTDOWN NOSAVE`: It immediately exists without final snapshot.
  * `SHUTDOWN NOW`: It skips the intail wait for replicas and shuts down immediately.
  * `SHUTDOWN FORCE`: It ignores any errors reported during shutdown process (such as failing to save RDB file due to permission issues).

**Note:**  
To start a Redis instance installed using Microsoft Archive in Windows, open **Services** window _(either search for **services** in your task bar search window to open it or press **Windows+R** key to open **Run** window and type `services.msc` and click **OK** to open it)_ and scroll down to look for **Redis** service and click on **Start** link.
