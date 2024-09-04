### 怎么实现单例模式
饿汉模式、懒汉模式、双重检测
**饿汉模式**
类加载时就初始化实例
**懒汉模式**
延迟加载，使用时才判断是否存在实例，但会有线程安全问题，需要引入锁机制保证线程安全，直接加锁会有性能问题
一般使用双重检测法，即先判断是否存在实例，若没有，则尝试获取锁，获取到锁之后还要进行一次判断，实例是否已经存在，不存在则初始化


JVM 中的堆和栈
堆用来存储对象实例，这里也是垃圾回收的主要区域，优势是可动态分配内存，但存取速度慢
栈中用来存储基本数据类型的变量以及对象的引用，存取数独快，但必须事先分配好内存大小

垃圾回收
在 java 8 中垃圾回年轻代和老年代采用不同的垃圾回收机制
年轻代中采用的是标记复制方法，内存空间被分为一个 Eden 区和两个Survivor 区，新建的对象被分配在 Eden 区，当被占满时进行 GC，将 Eden 区和Survivor 区中存活下来的对象复制到另一块Survivor 区中
老年代采用的是标记整理算法

FULL GC
触发时机：手动调用 System.gc ()，建议执行 full GC，但不一定执行，可以通过-XX:+ DisableExplicitGC 参数来禁止调用System.gc()

nginx
nginx 主要 3 大功能
web 服务器：nginx 配置中的 server 块


	server {
		listen 80;
		server_name localhost;
		root /var/ www/localhost;
		index index. html;
		 error_page 404 /404. html;
		location / {
			proxy_pass http://backend-servers ;
		}
	}
反向代理
通过 proxy_pass 配置将请求转到对应的 ip: 端口
负载均衡
通过 upstream 模块配置多个服务器，可以设置权重，nginx 会通过流量轮询访问不同的地址


http
超文本传输协议，是可靠数据传输协议，可靠性是通过传输层的 TCP 协议来实现的

https
http 的默认端口是 80，https 的默认端口是 443
http 运行在 TCP 协议之上，采用明文传输；https 是运行在 SSL/TLS 之上的 HTTP 协议。所有传输内容都经过加密。加密采用对称加密，但是对称加密的密钥用服务器方的证书进行了非对称加密。

对称加密和非对称加密
对称加密：密钥只有一个，加解密为同一个密码，且加解密速度快，如 DES、AES
非对称加密：密钥分为公钥和私钥，加解密使用不同的密钥，公钥加密需要私钥解密，私钥加密需要公钥解密。如 RSA，DSA 等

三次握手
建立 TCP 连接时，客户端和服务器总共发送 3 个包。3 次握手的主要作用是确认双方的接收能力和发送能力是否正常，指定自己的初始化序号为后面的可靠性传送做准备。
第一次：客户端发送一个SYN 报文，指明客户端的初始化序号 ISN，客户端处于 SYN_SEND 状态。
第二次：服务器收到后，以自己的 SYN 报文应答，指定自己的序号，同时把客户端的序号+1 作为 ACK 值，表示已经收到客户端的 SYN，服务器处于 SYN_RCVD 状态
第三次：客户端收到后，会发送一个 ACK 报文，同样将服务器的序号+1 作为 ACK 值，表示收到。
客户端处于ESTABLISHED 状态，服务器收到后也处于ESTABLISHED 状态，双方建立连接

两次握手可以吗
第一次客户端发送，服务器收到。服务器确认客户端的发送能力，服务器的接收能力
第二次服务器发送，客户端收到。客户端确认服务器的发送接收能力，客户端的接收能力，但服务器不知道客户端的接收能力需要客户端回应。
两次握手下，若客户端发出连接请求，由于网络延迟服务器没有收到并确认，客户端再次发送请求，此时服务器正常收到并确认建立了连接，数据传输完后连接关闭。后面前一次请求到了服务器，服务器收到后，如果没有第三次握手，向客户端发送应答后就建立连接，会浪费资源。

一个 http 请求的经过
浏览器输入 URL，先解析 URL 地址是否合法
浏览器检查是否有缓存，若有，直接展示
发送 http 请求之前，先进行 DNS 解析，获取域名的 IP 地址
浏览器向服务器发起 TCP 连接，建立 TCP 连接
握手成功后，浏览器向服务器发送 http 请求，请求数据包
服务器收到请求，将数据返回到浏览器
浏览器收到 http 响应
浏览器解析响应，若响应可以缓存，存入缓存
浏览器发送请求获取嵌入在 HTML 中的资源
浏览器发送异步请求渲染页面
最后页面全部渲染结束

跨域问题
源于浏览器的同源策略，即前端请求路径只能和当前页面的协议、域名、端口一致
解决方法
注解：@CrossOrigin
配置文件：创建一个配置文件，实现 WebMvcConfigurer 接口，并重写 addCrosMapping 方法
配置 CrosFilter 跨域：添加一个 Bean，配置返回 CrosFilter
Nginx 实现跨域：在 Nginx 的 Location 模块中配置添加头Access-Control-Allow-Origin
CrosWebFilter：添加 Bean 返回 CrosWebFilter

数据库优化
添加合适的索引
使用 join 代替子查询
如果是嵌套查询，尽力在子表中将数据范围缩小
导致索引失效的可能：like '%aaa%'不会使用索引，尽量不要在列上计算，不要使用 not in 和<>，使用 not exists 和< or >代替

HashMap
HashMap 基于 Hash 表实现，内部用一个数组存储元素，每个元素称为一个桶，每个桶中存储一个链表或红黑树，向 HashMap 中插入数据时，会先计算键的 Hash 值，然后映射到数组的索引位置。

怎么实现线程安全的 HashMap
hashMap 所有方法都不是同步的，多个线程同时修改数据可能会出现并发问题，具体是 put 数据时，会先计算 key 值的 Hash 值，如果两个线程 put 的数据 Hash 值一样，前一个线程已经完成 Hash 碰撞后，时间片用完，另一个线程也完成 Hash 碰撞并存入了值，这个线程后续会修改值，可能会覆盖原来的值。
 CuncorrentHashMap ：put 时采用 CAS 操作和 volatile 或synchronized 实现同步
	先计算 key 值是否已经存在，若 key 值为空则用 CAS 设置该节点的值，若已经存在则用synchronized 锁住桶，遍历桶中的元素，替换或新增节点中的数据
Collections.synchronizedMap：用于创建一个同步的Map, 对于修改 map 的方法，如 put、remove，使用同步块 synchronized 确保一次只有一个线程能访问

SpringBoot 注解
@Controller，@Service，@Bean，@Configuration，@Component 会创建 Bean 交给 spring 管理

mybatis 中#{}和${}
#{}会经过预编译，将参数使用? 表示，并通过 set 方法来绑定参数，所有参数都被当做字符串来处理
${}会直接将参数塞入 sql 中，并不加单引号，如果直接在参数中写 sql，可能会导致 sql 结构变化，导致 sql 注入

SpringBoot
@SpringBootApplication：@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan
使用@ControllerAdvice 开启全局异常处理，自定义方法使用@ExceptionHandler 注解，然后定义捕获异常的类型，就可以针对不同类型的异常进行处理

依赖注入： 
构造器注入，调用对象的构造函数注入
setter 注入，创建对象后，使用 set 方法，放入对象实例
字段注入，直接使用 @Autowired 直接在类的字段上注入

过滤器和拦截器
1.  ==‌**实现原理不同**‌==：
    - ==‌**过滤器**‌==：基于函数回调机制，通过实现`Filter`接口来定义过滤逻辑，通常用于Java EE应用中，依赖于Servlet容器。
    - ==‌**拦截器**‌==：基于Java的反射机制（动态代理），通常用于Spring MVC框架中，可以在方法执行前后添加额外的逻辑，不依赖于Servlet容器，可以在任何Java环境中使用。
2.  ==‌**使用范围不同**‌==：
    - ==‌**过滤器**‌==：可以对所有的请求进行拦截，包括HTML、CSS、JavaScript等静态资源请求。
    - ==‌**拦截器**‌==：主要用于拦截控制器方法，对JSP、HTML等静态资源请求不进行拦截，主要关注请求处理的前后环节。
3.  ==‌**执行顺序不同**‌==：
    - ==‌**过滤器**‌==：在请求进入Servlet容器后、进入Servlet之前进行预处理，请求结束时在Servlet处理完后返回给前端之前。
    - ==‌**拦截器**‌==：在请求进入Servlet后、进入Controller之前进行预处理，Controller渲染视图后请求结束。
4.  ==**注入Bean的情况不同**‌==：
    - ==‌**过滤器**‌==：无法注入Spring管理的Bean，因为过滤器不依赖于Spring容器。
    - ==‌**拦截器**‌==：可以注入Spring管理的Bean，因为拦截器是Spring提供并管理的。
5.  ==‌**触发时机不同**‌==：
    - ==‌**过滤器**‌==：在请求进入容器后立即触发，但在进入Servlet之前进行预处理。
    - ==‌**拦截器**‌==：在请求进入Servlet后、进入Controller之前进行预处理。
6.  ==‌**应用场景不同**‌==：
    - ==‌**过滤器**‌==：适用于通用的功能过滤，如敏感词过滤、响应数据压缩等。
    - ==‌**拦截器**‌==：适用于业务判断，如日志记录、权限判断等，更接近业务系统。

综上所述，过滤器和拦截器各有其适用场景和优势。过滤器更适合于通用的、不依赖于特定框架的预处理逻辑，而拦截器则更适合于Spring框架下的业务逻辑增强和AOP（面向切面编程）应用。


AOP 和IOC
AOP：面向切面编程，通过拦截器实现，对请求进入Controller 前或 Controller 结束后进行业务处理，可以用来做安全控制、日志管理，性能统计等。
包括通知、切点、切面概念。
通知：@Before、@After、@Around、@AfterReturning、@AfterThrowing

Spring 事务
声明式事务：@Transactional 注解开启一个事务
编程式事务：手动管理事务，注入PlatformTransactionManager，调用 getTransaction 方法开启事务，执行后调用 commit 或 rollback 结束事务，注意异常的处理，防止出现异常后事务没有提交也没有回滚导致数据库连接占满

SpringBoot 多线程
使用线程池
使用 @Async 注解开启异步操作，如果没有指定线程池会使用一个默认线程池，注意异步类需要是Spring 容器管理的 Bean

volatile
可见性、有序性、原子性
被它修饰的变量，每次取值都会从内存中重新读取
提供有序性，防止指令重排导致不可预料的结果
原子性，如 n = m + 1

synchronized


