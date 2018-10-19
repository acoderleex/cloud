
#1.synchronized 与 redis分布式锁
    
    SETNX

    GETSET

#2.Redis分布式锁

    支持分布式
    可以更细粒度的控制
    多台机器上多个进程对一个数据进行操作的互斥

#3.Redis缓存

    命中
    失效
    更新

    Cacheable
    CachePut
             这两个方式配合使用 但返回的对象要一致 而且必须要写不为空的一个key
             动态key的设置 key="#autokey" condition="#autokey.length()>3" unless="#result.getCode()!=0"  //unless(如果不)


    CacheEvict 删除缓存

    CacheConfig

#4.项目部署

    mvn clean package -Dmaven.test.skip=true

    finalName使用

    tomcat
    java -jar
    
#5.Spring Cloud
    
    服务发现 Netflix Eureka(基于REST 分为server和client两端)
    负载均衡 Netflix Ribbon(客户端可以通过微服务的名称来调用真实的微服务实例并实现相关的负载均衡策略)
    断路器   Netflix Hystrix(实现服务限流 熔断 降级)
                           出现错误之后可以返回fallback 信息
                           结合Feign一起使用时候 还需要进行Feign的熔断配置
                           服务降级(是在客户端实现的)
                    Hystrix DashBoard Turbine(基于Hytrix DashBoard 加密微服务的访问操作，需要这种安全策略,追加web的安全策略配置 WebSecurityConfig web.ignoring.anyMatchers("/hystrix.steam","/turbine.steam"))
                               
                           
    服务网关 Netflix Zuul(路由转发和过滤 默认和Ribbon结合实现负载均衡功能   一般接口不直接暴露给调用端使用 而是经过API网关根据相应请求路由到对应服务)
    分布式配置 Spring Cloud Config  进行配置文件(application.yml)的管理   git配置  
                                  application.yml是用户级别的资源配置
                                  bootstrap.yml是系统级别的配置 其优先级会更高一点
                                     
    消息总线   Spring Cloud Bus 必须要有一个明确的消息服务组件,而这种服务组件一般会有两种处理模式: RabbitMQ 和 Kafka
                           
                          /bus/*执行器命名空间下还有一些http端点。目前有两个实施。第一个/bus/env发送密钥/值对来更新每个节点的Spring环境。第二个，/bus/refresh，将重新加载每个应用程序的配置，就好像他们在他们的/refresh端点上都被ping过。     
                           
                          @RefreshScope注解 也不是所有的微服务配置都需要更新 所以要配置一个类  将所有可能动态获取的配置内容保存起来 以供到处引用
                               
                          本地系统如果想要进行刷新 ，用户必须要有ACTUATOR角色（WebSecutityConfig）
                          
    Spring Cloud Stream 就是使用基于消息系统的微服务处理架构
                        
                        
                                        
                           
    
#6.Spring Cloud 杂记 

    
    zipkin sleuth 追踪服务
    Gateway
    
    
    Eureka HA机制 
    
    CAP理论   一致性Consistency
             可用性Availability
             分区容错Partitiontolerance
        一个分布式系统最多只能满足其中两项
  
    从CAP理论角度说明Eureka(ap)和Zookper(cp)区别


    
    Feign   简化Controller中RestTemplte需要写入Eureka的微服务名称(url)还有多个参数 关键配置@FeignClient
            数据压缩
            也实现了负载均衡的功能
    
    Zuul的服务最终还是会注册到Eureka中@EnableZuulProxy
    
    application.yml
     zuul:
      prefix:   /mldn-proxy  //整个zuul的前缀   
      ignoredServices:   '*'
      routes:
        //进行url绑定
        company.path: /company-proxy/** //是在zuul中定义的映射路径
        company.url: http:company-8081.com:8081/company 不建议这样直接绑定 因为不利于负载均衡的处理
        //
        
    对于zuul本质就是一个代理操作,但是实际使用中微服务都有一个自己的认证信息,那么在这样的状态下，如果你当前所代理的微服务具有认证信息,那么就必须在其访问前追加认证的头部操作,
    这样就需要zuul的过滤操作完成
    
    Zuul fiterType  pre         在请求发出前被调用
                    route       在路由请求时候被调用
                    post        在路由之后发送请求信息时候被调用
                    error       在出现错误之后被调用

    Zuul 也需要安全访问 所以也应该在pom.xml追加Spring安全访问配置操作 并修改yml配置文件 追加用户信息配置
    
    security:
        base:
            enable: true
    user:
        name:  admin
        password: admin        
        
    以及过滤器的禁用    


    Zuul服务降级 ( Zuul 目前只支持服务级别的熔断，不支持具体到某个URL进行熔断  建议还是用Hytrix降级服务)
    
    Zuul Retry 
         开启重试在某些情况下是有问题的，比如当压力过大，一个实例停止响应时，路由将流量转到另一个实例，很有可能导致最终所有的实例全被压垮。说到底，断路器的其中一个作用就是防止故障或者压力扩散。用了retry，断路器就只有在该服务的所有实例都无法运作的情况下才能起作用。这种时候，断路器的形式更像是提供一种友好的错误信息，或者假装服务正常运行的假象给使用者。
         不用retry，仅使用负载均衡和熔断，就必须考虑到是否能够接受单个服务实例关闭和eureka刷新服务列表之间带来的短时间的熔断。如果可以接受，就无需使用retry。       
    
    csrf 跨站请求安全配置
    
    上传操作无法使用Feign 只能通过HttpClient完成模拟上传请求
    
        
        
         
        
    


