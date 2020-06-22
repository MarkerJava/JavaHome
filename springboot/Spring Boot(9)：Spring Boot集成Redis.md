### Spring Boot 集成Redis

*在Spring Boot 2.x之后，原来使用的Jedis被替换成了Iettuce*

### Jedis 和 Iettuce区别

* Jedis它是采用直连，多个线程操作的话是不安全的为了避免不安全，Redis使用Jedis pool连接池（像BIO模式）
* Iettuce采用net，实例可以再多个线程中共享，不存在安全的情况，可以减少线程数据（像NIO模式）

*Lettuce 是一个可伸缩线程安全的 Redis 客户端，多个线程可以共享同一个 RedisConnection，它利用优秀 netty NIO 框架来高效地管理多个连接。*

源码：

 ```java
	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate")//如果不存在就会生效，所有可以定义自己的redisTemplate替换默认的
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
    //默认的redisTemplate没有过多的设置，redis对象都是需要序列化
    //两个泛型都是Object，Object的类型，后使用需要强制转换
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

	@Bean
	@ConditionalOnMissingBean  //String是redis常用类型，所有它单独拿出来一个Bean
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

}
 ```

