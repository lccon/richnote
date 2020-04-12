###### 1.select 0-15：切换redis库；

###### 2.keys *：查看所有可以；

###### 3. FLUSHDB：清空数据库;

###### 4.FLUSHALL：清空所有数据库；

###### 5.move k1 2：将k1移动到2号库；

###### 6.事务执行步骤：

四种结果：正常执行，放弃事务，全体连坐(非运行时报错，都不执行)，冤头债主(运行时报错，只是报错的不执行)，watch监控；

①MULTI：开启事务；

②添加操作加入队列，如set k1 v1;

③EXEC：入栈；

④DISCARD：放弃事务；

⑤WATCH：监视key，如果在事务执行之前这个key被其他命令所改动，那么事务将被打断；

⑥UNWATCH：取消watch命令对所有key的监视；

###### 7.一主多从，主从复制，读写分离

①info replication：查看主机或是从机；

②slaveof 127.0.0.1 6379：设置从机跟随主机，只要连接成功，主从复制就开启；

③slaveof no one：使当前数据库停止与其他数据库同步，转为主数据库；

注意：

加入从机，从机也会将连接主机之前对主机操作的数据复制下来，全量复制，随后增量复制；

主机宕机，从机还是从机，等主机恢复自动连接主机，一样主从复制；

从机宕机，等从机恢复，将变成主机，可进行配置，恢复还是从机；

###### 8.哨兵模式

①创建sentinel.conf配置文件；

②sentinel monitor host主机名 127.0.0.1 6379 1；监控的主数据库，从机竞争多余1票选为主机；

注意：

主机宕机，从机之一选为主机，主机恢复，将自动配置为从机；

######  9.redis五大类型

String：key和value都是string类型；

​			**方法：**①set/get：存值/取值；

​						②append：在value后追加内容；

​						③incr/decr：在value为数字时，数字加或减1；如：incr k1；

​						④getrange/setrange：getrange是截取value个数，如：getrange k1 0 3，把k1的value截取0-3个						字符，setrange是替换字符串，如：setrange k1 0 xxx，把k1的value从0位开始替换为xxx；

​						⑤setex/setnx/del：setex是设置过期时间，如：setex k1 10 v1，k1的过期时间为10秒后；

​						setnx job "hello" :

​						如果当前job存在，则返回0，表明赋值不成功。

​						如果当前job不存在，则返回1，表明赋值成功。

​						del job: 单独删除操作命令

​						⑥mset/mget/msetnx：mset可以一次性插入多个key和value，mset k1 v1 k2 v2；

​						mget可以一次性获取多个key的值；

​						msetnx可以一次性插入多个，首先先做不存在判断，部分存在也是不能插入库中；

Hash：相当于Map<String, Object>，KV模式保持不变，但V是个键值对；

​			**方法:**   ①hset/hget：插入，获取，如hset user id 12，hget user id;

​							hmset/hmget：一次性插入多条记录，如hmset user id 13 name z3 age 17，hmget  user id 							name age;

​							hgetall：获取所有的hash键值对，hgetall user;

​							hdel：删除hash键值对，hdel user name;

​						②hlen：获取KV数量，hlen user;

​						③hexists：判断是否存在key，hexists id;

​						④hkeys/hvals：获取所有的key或value，如hkeys user，hvals user;

​						⑤hincrby/hincrbyfloat：value为数字或者小数时进行加减，如hincrby user age 3，	  	 	       							hincrbyfloat user score 0.5;

​						⑥hsetnx：判断不存在就插入，存在不做操作，如hsetnx user email 123@qq.com;

List：使用的LinkedList，双向链表形式，前后都可以插入数据；

​			**方法:**   ①lpush/rpush/lrange：lpush是先进后出，如：lpush list01 1 2 3 4 5，展示为5 4 3 2 1；

​						rpush:是先进先出，如：rpush list02 1 2 3 4 5，展示为1 2 3 4 5；	

​						lrange:查看范围，如：lrange list01 0 -1，0到-1范围区间是查看所有；

​						②lpop/rpop：lpop左出站，如：lpop list01，展示为5；rpop右出站，如：rpop list01，展示1；						从缓存中消失；

​						③lindex：查看角标索引值，如：lindex list01 2，查看list集合角标索引为2的数据；

​						④llen：查看list长度，如：llen list01；

​						⑤lrem：删除n个值，如：lrem list01 2 3；删除list01集合2个为3的value；

​						⑥ltrim：截取指定长度数据，如：ltrim list01 0 3；截取list01集合0-3长度数据，放入list01中；

​						⑦rpoplpush：将压栈放入另一个栈顶，如rpoplpush list01 list02，将list01的压栈放入list02集合						栈顶，并删除list01压栈元素；

​						⑧lset：将指定位置的数据改值，如lset list01 1 x，将list01的1位置的值改为x；

​						⑨linsert：将值插入value之前或之后，如linsert list01 before/after x java，将java插入x值的之前						或之后；

Set：无序无重复；

​			**方法:**	①sadd：添加set集合内容，如sadd set01 2 2 3 3 ;

​						smembers：查看set集合所有内容， 如smembers set01;

​						sismember：查看值是否存在set集合中，如sismember set01 2;

​						②scard：查看集合中有多少个元素，如scard set01;

​						③srem：删除集合中的元素，如srem set01 2;

​						④srandmember：随机抽取集合中指定个数元素，如srandmember set01 2，set01集合中随机取						两个元素；

​						⑤spop：随机出栈，如spop set01 1，随机出栈一个，从内存中删除；

​						⑥smove：从一个set集合中数据移动另一个set集合，如smove set01 set02 5；将set01集合中的5						移动到set02中；

​						⑦sdiff/sinter/sunion：sdiff，差集，在第一集合中而不再第二个集合中，如sdiff  set01 set02，在						set01集合中而不在set02中；sinter，交集；sunion，并集；

Sorted Set：有序无重复，value为键值对；

​			**方法:**   ①zdd：添加set集合，如zset zset01 60 v1 70 v2 80 v3;

​						zrange：查看所有的key和value，如zrange zset01 0 -1，查看所有的value，zrange zset01 0 -1 						withscores，查看所有的key和value；

​						②zrangebyscore：指定范围截取，如zrangebyscore zset01 60 70，zrangebyscore zset01 (60  						(80，不包含60和80，zrangebyscore zset01 60 80 limit 2 1，包含60和80从下标为2截取1个元素;

​						③zrem：删除set集合元素，如zrem zset01 v3;

​						④zcard/zcount：统计个数，如zcard zset01，zcount zset01 60 80，统计60到80的个数；

​							zscore/zrank：获取值或者获取下标，如zscore zset01 v4，zrank zset01 v4;

​						⑤zrevrank：逆序获取下标值，如zrevrank zset01 v4;

​						⑥zrevrange：逆序输出所有，如zrevrange zset01 0 -1;

​						⑦zrevrangebyscore：从结束到开始输出数据，如zrevrangebyscore zset01 80 60;