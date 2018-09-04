# 锁机制 

hap提供两种锁机制, 满足项目上对数据并发修改的需求.

## 1.数据库锁
***
根据适当的参数进行物理锁定（数据库级锁），支持3种数据库：oracle，mysql，sqlserver。提供 API, 自动执行 sql
> 
   mysql,orcale 执行 select * from xxx where ... for update 

>  sqlserver 执行 select * from xxx with (ROWLOCK) where ...

>  都是数据库原生的锁机制



使用方式：
### 单数据源
在Java代码中，注入`DatabaseLockProvider`类，

```java

public class XXXServiceImpl {

  @Autowired
  private DatabaseLockProvider databaseLockProvider;
   
  public int update (XXX dto, ...){

    // 传入锁定对象，锁定数据库相关行
    databaseLockProvider.lock(dto);

    //...
  }
}

```
### 多数据源

在Java代码中，`自己创建DatabaseLockProvider类`，指定`dataSource`

```java

public class XXXServiceImpl {

  @Autowired
  @Qualifier("xxxDataSource")
  private DataSource dataSource;

  private DatabaseLockProvider databaseLockProvider;
  
  @Transactional(propagation = Propagation.REQUIRED)
  public int update (XXX dto, ...){
   
    databaseLockProvider = new DatabaseLockProvider(dataSource)
    // 传入锁定对象，锁定数据库相关行
    databaseLockProvider.lock(dto);
    //...
  }
}

```

> 事务提交以后，锁会自动释放

DatabaseLockProvider提供的接口

```java

//根据传入对象，自动加锁。
//确保bean上有@Table 注解，能自动解析出对应的表。 默认根据有@ID注解的字段进行加锁，确保id有值
public void lock(Object dto) 

//根据传入对象和自定义的where条件，自动加锁。
//传入类似 "name = ? and age = ?" 的条件语句，根据whereCondition，依次对应传入属性对应的值。
public void lock(Object dto, String whereCondition, Object... whereParameter)

// 可以自定义表名，不必传入dto
public void lock(String tableName, String whereCondition, Object... whereParameter)


```

## 2.分布式锁

 基于`redis`的锁，实现跨平台, 分布式锁

使用方式：
 
 1.根据实际配置的redis，修改配置文件`config.properties`
```properties
redisson.server.url=127.0.0.1:6379
#redissson.server.password=
```
> 支持`多节点`配置，按照如下规范配置
 
>redisson.server.url=\

>    127.0.0.1:6379,\

>    10.211.103.142:6379

>内部使用redisson提供的`RedLock`进行加锁

 2.在java代码中注入DistributedLockProvider

```java
public class XXXServiceImpl {

 @Autowired
 @Qualifier("distributeLockTemplate")
 private DistributedLockProvider distributedLockProvider;

 public int update (XXX dto, ...){
   
     //使用回调方式，执行自己的业务逻辑
     distributedLockProvider.lock(dto,new DistributedLockCallback<Object>() {
                @Override
                public Object process() {
                   //doSomething();
                   return null;
                })
     }
}
```

> 回调函数执行以后，锁都会自动释放

DistributedLockProvider提供的接口：
```java
    /**
     * 使用分布式锁，使用锁默认超时时间。
     *
     * @param lockKey
     *            加锁对象，确保有@ID属性，建议BaseDto
     * @param callback
     * @return    回调函数返回的内容
     */
    <T> T lock(Object lockKey, DistributedLockCallback<T> callback);

    /**
     * 使用分布式锁，使用锁默认超时时间。
     *
     * @param lockKey
     *            确保唯一
     * @param callback
     * @return    回调函数返回的内容
     */
    <T> T lock(String lockKey, DistributedLockCallback<T> callback);

    /**
     * 使用分布式锁。自定义锁的超时时间
     *
     * @param lockKey
     *            加锁对象，确保有@ID属性，建议BaseDto
     * @param callback
     * @param leaseTime
     *            锁超时时间。超时后自动释放锁。
     * @param timeUnit
     * @return    回调函数返回的内容
     */
    <T> T lock(Object lockKey, DistributedLockCallback<T> callback, long leaseTime, TimeUnit timeUnit);

    /**
     * 使用分布式锁。自定义锁的超时时间
     *
     * @param lockKey
     *            确保唯一
     * @param callback
     * @param leaseTime
     *            锁超时时间。超时后自动释放锁。
     * @param timeUnit
     * @return    回调函数返回的内容
     */
    <T> T lock(String lockKey, DistributedLockCallback<T> callback, long leaseTime, TimeUnit timeUnit);

    /**
     * 使用分布式锁，使用锁默认超时时间。
     *
     * @param lockKey
     *            确保唯一
     * @param callback
     * @return    回调函数返回的内容
     *
     * @exception Exception 加锁过程的异常，比如锁已被占用，加锁失败
     */
    <T> T tryLock(String lockKey, DistributedLockCallback<T> callback) throws Exception;

    /**
     * 使用分布式锁，使用锁默认超时时间。默认等待时间1秒
     *
     * @param lockKey
     *            加锁对象，确保有@ID属性，建议BaseDto
     * @param callback
     * @return    回调函数返回的内容
     *
     * @exception Exception 加锁过程的异常，比如锁已被占用，加锁失败
     */
    <T> T tryLock(Object lockKey, DistributedLockCallback<T> callback) throws Exception;

    /**
     * 使用分布式锁。自定义锁的超时时间
     *
     * @param lockKey
     *            加锁对象，确保有@ID属性，建议BaseDto
     * @param callback
     * @param waitTime
     *            获取锁等待时间，超过设置时间未获得锁则抛出异常
     * @param leaseTime
     *            锁超时时间。超时后自动释放锁。
     * @param timeUnit
     *            waitTime，leaseTime 时间单位
     * @return    回调函数返回的内容
     *
     * @exception Exception 加锁过程的异常，比如锁已被占用，加锁失败
     */
    <T> T tryLock(Object lockKey, DistributedLockCallback<T> callback, long waitTime, long leaseTime, TimeUnit timeUnit)
            throws Exception;

    /**
     * 使用分布式锁。自定义锁的超时时间
     *
     * @param lockKey
     *            确保唯一
     * @param callback
     * @param waitTime
     *            获取锁等待时间，超过设置时间未获得锁则抛出异常
     * @param leaseTime
     *            锁超时时间。超时后自动释放锁。
     * @param timeUnit
     *            waitTime，leaseTime 的时间单位
     * @return    回调函数返回的内容
     *
     * @exception Exception 加锁过程的异常，比如锁已被占用，加锁失败
     */
    <T> T tryLock(String lockKey, DistributedLockCallback<T> callback, long waitTime, long leaseTime, TimeUnit timeUnit)
            throws Exception;
    

```
