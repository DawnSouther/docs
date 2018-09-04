# 配置维护

配置：PROFILE。类似 EBS 中的系统参数。可以有三个维度
1. 系统（GLOBAL）
2. 角色（Role）
3. 用户（User）

我们可以定义一个 *配置* ，他可以在不同维度有不同的值。

#### 举个例子：币种

假设系统支持多种币种：CNY,EUR,HKD,GBP

用户可以根据自的偏好，选择不同币种作为货币展示单位。

那么，就可以定义一个配置：`CURRENCY`

* 可以设置 CURRENCY 在 *GLOBAL级别* 的值是：CNY

表示，系统默认使用 CNY 作为币种。

* 设置 CURRENCY 在 *角色级别* 有不同的值：
  * 角色1，对应的值是 EUR，表示 角色1 使用 EUR 作为默认币种
  * 角色2，对应的值是 USD，表示 角色2 使用 USD 作为默认币种
  * 以此类推，每个角色都可以有不同的值，也可以不设置

* 设置 CURRENCY 在 *用户级别* 有不同的值：
  * 用户1，对应的值是 GBP，表示 用户1 使用 GBP 作为默认币种

  和角色类似，每个用户，都可以设置不同的值，也可以不设置。

> 好了，现在，任意一个用户（不管设置在哪个级别，最终都要落到用户上），都会有一个 CURRENCY 值和他对应。

1. 用户1 对应 GBP，这是设置好的
2. 用户2 没有设置 *用户级别* 的值，那就取 用户2 当前角色（假设为 角色2） 的值：USD
3. 用户3 没有设置 *用户级别* 的中，他的角色（假设为 角色3）也没有设置 *角色级别* 的值，那就取*GLOBAL级别*的值：CNY

以上3条规则，就可以看到，所有的用户，最终都会有一个具体值和他对应。

在实际使用场景中，可以通过 ProfileService 的 API，取到某个用户在 CURRENCY 上对应的值，然后做不同的处理。




## 系统级配置特殊用途
***

HAP 框架可以利用 *系统级*的配置文件，做一些参数的实时同步。

> 举个例子：密钥参数

假设我们调用外部系统的 API，这些 API 需要用到 一些密钥对数据进行加解密。

这些参数 一般是由对方提供，并且有可能经常更新。

* 若我们把这些参数写死到 config.properties 中，一方面不安全，另一方面不方便更新（需要重启系统）。
* 建表，保存到数据库中，需要避免每次读取数据库，还要保证每次都使用最新值，比较麻烦。

这时候可以利用系统级别的配置，定义一个配置：XXX_SECRET
* 他只有一个 GLOBAL 级别的值：123fjkah123sf21

然后在 java 代码中
```java

public class XXXEncryptor implements GlobalProfileListener {
  private String secret;

  @Override
  public List<String> getAcceptedProfiles(){
    return Arrays.asList("XXX_SECRET"); // 可以监听多个 PROFILE
  }
  @Override
  public void updateProfile(String profileName, String profileValue){
    this.secret = profileValue; // 监听多个 PROFILE 时，注意 profileName 会不同
  }
  
  public byte[] encrypt(byte[] data) {
    // use secret ,do something
    return data;
  }
}

```

把这个XXXEncryptor 通过 spring 配置文件，定义一个 bean(也可以通过注解自动扫描注册)。

GlobalProfileListener 起到的作用：
1. 在系统启动时，加载指定的配置，设置到类中
2. 在更新指定的配置文件时，会自动通知所有相关监听者（listener），进行更新
  * 适用于集群环境 

也就是说，java 代码中，我们每次使用的都是最新的值，且不需要读取数据库。

关于这个机制的原理，可以参考 [消息机制](message.md)
