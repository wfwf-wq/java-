同步异步、阻塞非阻塞

#### 同步、异步

**同步**：用户发起一个调用后，被调用者未处理完请求之前调用不返回。

**异步**：发起一个调用后，立刻得到被调用者的回应表示已经接收到请求，但是被调用者并不返回结果，此时我们可以处理其他请求，被调用者通过事件，回调等机制通知调用者返回结果。

异步调用者不用等待处理结果。

#### 阻塞、非阻塞

**阻塞**：发起一个请求，调用者一直等待请求结果返回，也就是当前线程会被挂起，无法从事其他任务，只有当条件就绪才能继续。

**非阻塞**：发起一个请求，调用者不用一直等着结果返回，可以先去干其他事情。

### BIO、NIO、AIO

#### BIO

BIO在不考虑多线程的情况下，是无法处理并发。

原因：

1. serverSocket.accept阻塞。
2. socket.getInputStream.read 阻塞。

#### NIO

因为线程会阻塞，导致其他线程无法进入。出现了NIO。

# 分布式系统

## MQ

### 为什么使用消息队列？

#### 解耦

**不用MQ的系统耦合场景**

![image-20200615192733009](D:\github\java-\java架构.assets\不用MQ的耦合.png)



当后面系统不断增加，比如 E，F系统的加入，以及D系统的移除。

![image-20200418213021225](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200418213021225.png)



 **使用了MQ之后的解耦场景：**

![使用MQ的解耦](D:\github\java-\java架构.assets\使用MQ的解耦.png)





总结：通过一个MQ，发布和订阅模型，Pub/Sub模型，系统A就和其它系统彻底解耦。

#### 异步

 **不用MQ的同步高延时请求场景**

![image-20200419205855859](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200419205855859.png)

**使用MQ进行异步化**

![image-20200419213232855](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200419213232855.png)

系统A只需要发送消息到MQ中就直接返回了，然后其它系统各自在MQ中进行消费。用户在执行系统A的时候，就会感觉非常快就得到响应了。

#### 削峰

**没有MQ的削峰**：

![image-20200419213609511](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200419213609511.png)

一般的MySQL，抗到QPS=2000的时候就已经达到了瓶颈，如果每秒请求达到了5000的话，可能直接就把MySQL打死了。如果MySQL被打死，然后整个系统就崩溃，然后系统就没法使用。

但是中午的高峰期过了之后，到下午的时候，就成了低峰期，可能也就一万用户同时在网站上操作，每秒的请求数量可能就50个请求，对整个系统几乎没有任何压力。



**使用MQ来进行削峰**

![image-20200419235201993](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200419235201993.png)

### 引入MQ后存在的一些问题

1. 系统可用性降低：系统引入的外部依赖越多，越容易挂掉，本来你就是A系统调用BCD三个系统接口就好了，人家ABCD四个系统好好的，没啥问题，这个时候却加入了MQ进来，万一MQ挂了怎么办？MQ挂了整套系统也会崩溃了。
2. 系统复杂性提高：硬生生加个MQ进来，你怎么保证消息没有重复消费？怎么处理消息丢失的情况？怎么保证消息传递的顺序性？
3. 一致性问题：A系统处理完了直接返回成功了，人都以为你的请求成功了，但是问题是，要在BCD三个系统中，BD两个系统写库成功了，结果C系统写库失败了，这样就会存在数据不一致的问题。



![image-20200420070841754](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420070841754.png)

### 主流MQ的对比

主流MQ主要有：kafka、activemq、rabbitmq和rocketmq

| 特性       | ActiveMQ                                                     | RabbitMQ                                                     | RocketMQ                                                     | Kafka                                                        |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 单机吞吐量 | 万级，吞吐量比RocketMQ和Kafka要低一个数量级                  | 万级，吞吐量比RocketMQ和Kafka要低一个数量级                  | 10万级，RocketMQ也是可以支撑高吞吐的一种MQ                   | 10万级1这是kafka最大的优点，就是吞吐量高。一般配置和数据类的系统进行实时数据计算、日志采集等场景 |
| 时效性     | ms级                                                         | 微妙级，这是RabbitMQ的一大特点，就是延迟最低                 | ms级                                                         | 延迟在ms级内                                                 |
| 可用性     | 基于主从架构实现高可用                                       | 高，基于主从架构实现高可用                                   | 非常高，分布式架构                                           | 非常高，kafka是分布式的，一个数据多个副本，少数机器宕机后，不会丢失数据，不会导致不可用 |
| 消息可靠性 | 有较低的概率丢失数据                                         | 消息不丢失                                                   | 经过参数优化配置，可以做到0丢失                              | 经过参数优化配置可以做到0丢失                                |
| 核心特点   | MQ领域的功能及其完备                                         | 基于Erlang开发，所以并发能力强，性能及其好，延时很低         | MQ功能较为完善，还是分布式的，扩展性好                       | 功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用，是实时上的标准。 |
| 优劣势总结 | 非常成熟，功能强大，在业内大量公司以及项目都有应用。 但是偶尔消息丢失的概率，并且现在社区以及国内应用都越来越少，官方社区对ActiveMQ5.X维护越来越少，而且确实主要是基于解耦和异步来用的，较少在大规模吞吐场景中使用 | erlang语言开发的，性能及其好，延时很低。而且开源的版本，就提供的管理界面非常棒，在国内一些互联网公司近几年用RabbitMQ也是比较多一些，特别适用于中小型的公司 缺点显而易见，就是吞吐量会低一些，这是因为它做的实现机制比较中，因为使用erlang开发，目前没有多少公司使用其开发。所以针对源码界别的定制，非常困难，因此公司的掌控非常弱，只能依赖于开源社区的维护。rabbitmq集群扩展麻烦一点。 | 接口简单易用，毕竟在阿里大规模应用过，有阿里平台保障，日处理消息上 百亿之多，可以做到大规模吞吐，性能也非常好，分布式扩展也很方便，社区维护还可以，可靠性和可用性都是OK的，还可以支撑大规模的topic数量，支持复杂MQ业务场景。 | 仅仅提供较少的核心功能，但是提供超高的吞吐量，ms级别的延迟，极高的可用性以及可靠性，分布式可以任意扩展。 同时kafka最好是支撑较少的topic数量即可，保证其超高的吞吐量。 |



1. 一般的业务要引入MQ，最早大家都是用ACviceMQ，但是现在大家用的不多了，没有经过大规模吞吐量场景的验证，社区也不是很活跃，所以大家还是算了，不太图鉴使用
2. RabbitMQ后面被大量的中小型公司所使用，但是erlang语言阻碍了大量的Java工程师深入研究和掌握它，对公司而言，几乎处于不可控的状态，但是RabbitMQ目前开源稳定，活跃度也表较高。
3. RocketMQ是阿里开源的一套消息中间件，目前也已经经历了天猫双十一，同时底层使用Java进行开发

如果中小型企业技术实力一般，技术挑战不是很高，可以推荐，RabbitMQ。如果公司的基础研发能力很强，想精确到源码级别的掌握，那么推荐使用RocketMQ。同时如果项目是聚焦于大数据领域的实时计算，日志采集等场景，那么Kafka是业内标准。



### 如何保证消息队列的高可用？

**剖析**

这个问题用的很好，不会具体到某个MQ，而是问一个整体，然后通过你使用的MQ，来具体谈谈该MQ的可用性的理解。

#### RabbitMQ高可用性

RabbitMQ是比较有代表性的，因为是基于主从做高可用性的。

RabbitMQ 三种模式：单机模式，普通集群模式，镜像集群模式



##### **单机模式**

就是demo级别的，一般就是本地启动后玩一玩，没有人生产环境中使用。



##### 普通集群模式

意思就是在多台机器上启动多个RabbitMQ实例，每台机器启动一个，但是创建的Queue，只会放在一个RabbitMQ实例上，但是每个实例都同步queue元数据，在消费的时候，实际上是连接到另外一个实例上，那么这个实例会从queue所在实例上拉取数据过来，这种方式确实很麻烦，也不怎么好，没做到所谓的分布式 ，就是个普通集群。因为这导致你要么消费每次随机连接一个实例，然后拉取数据，要么固定连接那个queue所在实例消费数据，前者有数据拉取的开销，后者导致单实例性能瓶颈。



而且如果那个放queue的实例宕机了，会导致接下来其它实例无法从那个实例拉取，如果 你开启了消息持久化，让rabbitmq落地存储消息的话，消息不一定会丢，得等到这个实例恢复了，然后才可以继续从这个queue拉取数据。



![image-20200420091806944](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420091806944.png)

这里没有什么所谓的高可用性可言，这个方案主要就是为了解决吐吞量，就是集群中的多个节点来服务于某个queue的读写操作。



**存在两个缺点**

- 可能会在RabbitMQ中存在大量的数据传输
- 可用性没有什么保障，如果queue所在的节点宕机，就会导致queue的消息丢失
- 

##### 镜像集群模式

这种模式，才是RabbitMQ的高可用模式，和普通的集群模式不一样的是，你创建的queue无论元数据还是queue里的消息都会存在与多个实例中，然后每次你写消息到queu的时候，都会自动把消息推送到多个实例的queue中进行消息同步。

这样的好处在于，你任何一个机器宕机了，别的机器都可以用。坏处在于，性能开销提升，消息同步所有的机器，导致网络带宽压力和消耗增加，第二就是没有什么扩展性科研，如果某个queue负载很重，你加机器，新增的机器也包含了这个queue的所有数据，并没有办法线性扩展你的queue

那么如何开启集群镜像策略呢？就是在RabbitMQ的管理控制台，新增一个策略，这个策略就是镜像集群模式下的策略，指定的时候，可以要求数据同步到所有的节点，也可以要求就 同步到指定数量的节点，然后再次创建queue的时候，应用这个策略，就会自动将数据同步到其它节点上去了

![image-20200420102752707](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420102752707.png)



集群镜像模式下，任何一个节点宕机了都是没问题的，因为其他节点还包含了这个queue的完整的数据，别的consumer可以到其它活着的节点上消费数据。

但是这个模式还存在问题：就是不是分布式的，如果这个queue的数据量很大，大到这个机器上的容量无法容纳的时候，此时应该怎么办呢？

#### kafka的高可用性

![image-20200420104251328](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420104251328.png)

kafka一个最基本的架构认识：多个broker组件，每个broker是一个节点，你创建一个topic，这个topic可以划分成多个partition，每个partition可以存在于不同的broker上，每个partition就放一部分数据。

这就是天然的分布式消息队列，就是说一个topic的数据，是分散在多个机器上的，每个机器上就放一部分数据。

实际上RabbitMQ之类的，并不是分布式消息队列，他就是传统的消息队列，只不过提供了一些集群、HA的机制而已，因为无论怎么玩，RabbitMQ一个queue的数据都放在一个节点里了，镜像集群下，也是每个节点都放这个queu的完整数据。

kafka0.8以前，是没有HA机制的，就是任何一个broker宕机了，那个broker上的partition就废了，没法读也没办法写，没有什么高可用可言，而在0.8版本后，提供了HA机制，就是replica副本机制，每个partition的数据都会同步到其它机器上，形成自己的多个replica副本，然后所有的replica就是follower，写的时候，leader会负责数据都同步到所有的follower上，读的时候就直接读取leader上的数据即可。只能读写leader？很简单，要是你能随意读写每个follower，那么就需要保证数据一致性的问题，系统复杂度太高，很容易出问题，kafka会均匀的将一个partition的所有replica分布在不同的机器上，这样才能够提高容错性

![image-20200420105712380](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420105712380.png)



同时多个副本中，会选取一个作为leader，其它的副本是作为follower，并且只有leader能对外提供读写，同时leader在写入数据后，它还会把全部的数据同步到follower中，保证数据的备份。

此时，高可用的架构就出来了，假设现在某个机器宕机了，比如其中的一个leader宕机了，但是因为每个leader下还有多个follower，并且每个follower都进行了数据的备份，因此kafka会自动感知leader已经宕机，同时将其它的follower给选举出来，作为新的leader，并向外提供服务支持。

### 如何保证消息的重复消费？

面试题：如何保证消息的重复消费？如何保证消息消费的幂等性？

**剖析**：其实这是一个常见的问题，既然是消费消息，那肯定是要考虑会不会重复消费？能不能避免重复消费？或者重复消费了也别造成系统异常可以吗？关于消息重复消费的问题，其实本质上就是问你使用消息队列如何保证幂等性，这个是你架构中要考虑的问题。

首先是比尔RabbitMQ、RocketMQ、Kafka都会出现消息重复消费的问题，因为这个问题通常不是MQ自己保证的，而是保证消息的不丢失，我们首先从Kafka上来说：

kafka实际上有个offset的概念，就是每个消息写进去，都有一个offset，代表他的序号，然后consumer消费了数据之后，每隔一段时间，会把自己消费过的消息offset提交一下，代表我已经消费过了，下次我要是重启啥的，你就让我从上次消费到的offset来继续消费。

但是凡事总有以外，比如我们之前生产经常遇到的，就是你有时候重启系统，看你怎么重启，如果碰到着急的，直接kill杀死进程，然后重启，这就会导师consumer有些消息处理了没来得及提交offset，然后重启后，就会造成少数消息重复消费的问题。



重复消费不可怕，重要的是有没有考虑过重复消费之后，怎么保证幂等性？

例如：有个系统，消费一条数据往数据库插入一条，要是消息重复消费了两次，那么就插入两条数据了，这个数据也就出错了。

![image-20200420112217458](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420112217458.png)

消费者如果在准备提交offset，但是还没有提交的时候，消费者进程被重启，那么此时已经消费过数据的offset并没有提交，kafka也就不知道你已经消费了，那么消费者再次上线进行消费的时候，会把已经消费的数据，重新在传递过来，这就是消息重复消费的问题。

#### 幂等性是什么？

通俗点说：幂等性就是一个数据，或者一个请求，给你执行多次，得保证对应的数据不会改变，并且不能出错，这就是幂等性。

### 怎么保证消息队列消费的幂等性？

一条数据重复出现两次，但是数据库里只有一条数据，这就保证了系统的幂等性。

**解决思路**

- 比如那个数据要写库，首先根据主键查一下，如果这个数据已经有了，那就别插入了，执行update即可

- 如果用的是redis，那就没问题了，因为每次都是set操作，天然的幂等性

- 如果不是上面的两个场景，那就做的稍微复杂一点，需要让生产者发送每条消息的时候，需要加一个全局唯一的id，类似于订单id之后的东西，然后你这里消费到了之后，先根据这个id去redis中查找，之前消费过了么，如果没有消费过，那就进行处理，然后把这个id写入到redis中，如果消费过了，那就别处理了，保证别重复消费相同的消息即可。

- 还有比如基于数据库唯一键来保证重复数据不会重复插入多条，我们之前线上系统就有这个问题，就是拿到数据的时候，每次重启可能会重复，因为Kafka消费者还没来得及添加offset，重复数据拿到了以后，我们进行插入的时候，因为有了唯一键约束了，所以重复数据只会插入报错，不会导致数据库中出现脏数据。

  ![image-20200420113844967](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420113844967.png)

### 如何保证消息传输不丢失？

面试题：如何保证消息的可靠性传输（如何处理消息丢失的问题）？

不能多，指的就是刚刚提到的重复消费和幂等性问题，不能少，指的是数据在传输过程中，不会丢失。

如果说使用MQ用来传递非常核心的消息，比如说计费，扣费的一些消息，比如设计和研发一套核心的广告平台，计费系统是一个很重的业务，操作是很耗时的，所以说广告系统整体的架构里面，实际是将计费做成异步化的，然后中间就是加了一个MQ。例如在广告主投放了一个广告，约定的是每次用户点击一次就扣费一次，结果是用户动不动就点击了一次，扣费的时候搞的消息丢了，公司就会不断的少几块钱。这样积少成多，这就是造成了公司的巨大损失。

#### 为什么会丢数据

丢数据，一般分为两种，要么是MQ自己弄丢了，要么是我们消费的时候弄丢了。我们可以从RabbitMQ和Kafka分别来进行分析。

RabbitMQ一般来说都是承载公司的核心业务的，数据是绝对不能弄丢的。

![image-20200420120701475](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420120701475.png)



#### 生产者丢失了数据

生产者将数据发送到RabbitMQ的时候，可能数据就在半路给搞丢了，因为网络啥的问题，都有可能。

此时i选择用RabbitMQ提供的事务功能，就是生产者发送数据之前，开启RabbitMQ事务（channel.txSelect），然后发送消息，此时就可以回滚事务（channel.txRollback），然后重试发送消息，如果收到了消息，那么可以提交事务，但是问题是，RabbitMQ事务机制一搞，基本上吞吐量会下来，因为太损耗性能。

![image-20200420121835297](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420121835297.png)

所以一般来说，如果你要确保写RabbitMQ消息别丢，可以开启confirm模式，在生产者那里设置了开启confirm模式之后，RabbitMQ会给你回传一个ack消息，告诉你这个消息OK了，如果RabbitMQ没能处理这个消息，会给你回调一个接口，告诉你这个消息接收失败，你可以重试

```
// 开启事务
try {
 // 发送消息
} catch(Exception e) {
 // 重试发送消息
}
//  提交
```

但是，因为事务机制，是同步的

针对于上述事务造成性能下降的问题，下面的方法是开启confirm模式

- 首先把channel设置成confirm模式
- 然后发送一个消息
- 发送完消息之后，就不用管了
- RabbitMQ如果接收到这个消息的话，就会回调你生产者本地的一个接口，通知你说这条消息我们已经收到了
- RabbitMQ如果在接收消息的时候出错了，就会回调这个接口

一般生产者如果要保证消息不丢失，一般是用confirm机制，因为是异步的模式，在发送消息之后，不会阻塞，直接可以发送下一条消息，这样吞吐量会更高一些。



####  RabbitMQ丢失数据

这个就是RabbitMQ自己丢失数据，这个时候就必须开启RabbitMQ的持久化，就是消息写入之后，同时需要持久化到磁盘中，哪怕是RabbitMQ自己宕机了，也能够从磁盘中读取之前存储的消息，这样数据一般就不会丢失了，但是存在一个极端的情况，就是RabbitMQ还没持久化的时候，就已经宕机了，那么可能会造成少量的数据丢失，但是这个概率是比较小的。

设置持久化的两个步骤，第一个是创建queue的时候，将其持久化的，这样就保证了RabbitMQ持久化queue的元数据，但是不会持久化queue中的数据，第二个就是发送消息的时候，将消息的deliveryMode设置为2，就是将消息设置为持久化的，此时RabbitMQ将会将消息持久化到磁盘上，必须同时设置两个持久化才行，哪怕是Rabbit挂了，也会从磁盘中恢复queue 和 queue中的数据。

而且持久化可以跟生产者那边的confirm机制配置起来，只有消息被持久化到磁盘后，才会通知生产者ACK了，所以哪怕是在持久化磁盘之前，RabbitMQ挂了，数据丢了，生产者收不到ACK，你也是可以自己重发的。



#### 消费者丢失数据

消费者丢失数据，主要是因为打开了AutoAck的机制，消费者会自动通知RabbitMQ，表明自己已经消费完这条数据了，但是如果你消费到了一条消息，还在处理中，还没处理完，此时消费者就会自动AutoAck了，通知RabbitMQ说这条消息已经被消费了，此时不巧的是，消费者系统宕机了，这条消息就会丢失，因为RabbitMQ以为这条消息已经处理掉。

在消费者层面上，我们需要将AutoAck给关闭，然后每次自己确定已经处理完了一条消息后，你再发送ack给RabbitMQ，如果你还没处理完就宕机了，此时RabbitMQ没收到你发的Ack消息，然后RabbitMQ就会将这条消息分配给其它的消费者去处理。

### kafka

#### 消费端弄丢了数据

你消费到了这个消息，然后消费者自动提交offset，kafka以为你已经消费好了这个消息，其实你是刚准备处理这个消息，然后你自己挂了。

所以关闭自动提交offset。

#### kafka自己搞丢数据

![kafka自己搞丢1数据](D:\github\java-\java架构.assets\kafka自己搞丢数据.png)







当broker 1 宕了以后，broker2将从follower变为leader，这个时候传入broker1的数据1就丢了，消费者就拿不到了。

一般要设置几个参数如下：

1. 给topic设置replication.factor参数：这个值必须大于1，要求每个partition必须至少2个副本。
2. 在kafka服务端设置min.insync.replicas参数：这个值必须大于1，这个是要求leader至少感知一个follower还跟自己联系，没掉队，才能确保挂掉了还有一个follower。
3. 在producer端设置acks=all：这个是要求每条数据，必须是写入所有replica之后，才能认为是写成功了。
4. 在producer端设置retries=max：要求一旦写入失败，就无限重试，卡在这里了。

#### 生产者不会丢数据

因为会无限次重试。



### 如何保证消息的顺序性？

#### 场景

以前做过一个MySQL binlog同步系统，压力还是非常大的，日同步数据要达到上亿。常见一点的在于 大数据项目中，就需要同步一个mysql库过来，然后对公司业务的系统做各种的复杂操作。

在mysql里增删改一条数据，对应出来的增删改3条binlog，接着这三条binlog发送到MQ里面，到消费出来依次执行，这个时候起码得保证能够顺序执行，不然本来是：增加、修改、删除，然后被换成了：删除、修改、增加，不全错了呢。

本来这个数据同步过来，应该是最后删除的，结果因为顺序搞错了，最后这个数据被保留了下来，数据同步就出错

- RabbitMQ：一个queue，多个consumer，这不明显乱了
- Kafka：一个topic，一个partition，一个consumer，内部多线程，就会乱套

在消息队列中，一个queue中的数据，一次只会被一个消费者消费掉

![image-20200420150800480](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420150800480.png)

但因为不同消费者的执行速度不一致，在存入数据库后，造成顺序不一致的问题

![image-20200420150935355](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420150935355.png)

#### RabbitMQ保证消息顺序性

RabbitMQ：拆分多个queue，每个queue一个consumer，就是多一些queue而已，确实是麻烦，或者就是一个queue，但是对应一个consumer，然后这个consumer内部用内存队列做排队，然后分发给底层不同的worker来处理。

下图为：一个consumer 对应 一个 queue，这样就保证了消息消费的顺序性。

![image-20200420151354856](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420151354856.png)

#### Kafka保证消息消息顺序性

一个topic，一个partition，一个consumer，内部单线程消费，写N个内存，然后N个线程分别消费一个内存queu即可。注意，kafka中，写入一个partition中的数据，一定是有顺序的，

![image-20200420152349066](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420152349066.png)

但是在一个消费者的内部，假设有多个线程并发的进行数据的消费，那么这个消息又会乱掉

![image-20200420152542344](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420152542344.png)

这样时候，我们需要引入内存队列，然后我们通过消息的key，然后我们通过hash算法，进行hash分发，将相同订单key的散列到我们的同一个内存队列中，然后每一个线程从这个Queue中拉数据，同一个内存Queue也是有顺序的。

![image-20200420153622880](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420153622880.png)



### 百万消息积压在队列中如何处理？

如何解决消息队列的延时以及过期失效问题？消息队列满了以后该怎么处理？有百万消息积压接小时，说说解决思路？

#### 场景1：积压大量消息

几千万的消息积压在MQ中七八个小时，这也是一个真实遇到过的一个场景，确实是线上故障了，这个时候要不然就是修复consumer，让他恢复消费速度，然后傻傻的等待几个小时消费完毕，但是很显然这是一种比较不机智的做法。

假设1个消费者1秒消费1000条，1秒3个消费者能消费3000条，一分钟就是18万条，1000万条也需要花费1小时才能够把消息处理掉，这个时候在设备允许的情况下，如何才能够快速处理积压的消息呢？

一般这个时候，只能够做紧急的扩容操作了，具体操作步骤和思路如下所示：

- 先修复consumer的问题，确保其恢复消费速度，然后将现有consumer都停止
- 临时建立好原先10倍或者20倍的queue数量
- 然后写一个临时的分发数据的consumer程序，这个程序部署上去消费积压的数据，消费之后不做耗时的处理，直接均匀轮询写入临时建立好的10倍数量的queue
- 接着临时征用10倍机器来部署consumer，每一批consumer消费一个临时queue的数据
- 这种做法相当于临时将queue资源和consumer资源扩大了10倍，以正常的10倍速度

![image-20200420160304030](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420160304030.png)

也就是让消费者把消息，重新写入MQ中，然后在用 10倍的消费者来进行消费

![image-20200420160319662](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/1_%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200420160319662.png)

#### 场景2：大量消息积压，并且设置了过期时间

假设你用的是RabbitMQ，RabbitMQ是可以设置过期时间的，就是TTL，如果消息在queue中积压超过一定的时间，就会被RabbitMQ给清理掉，这个数据就没了。这个时候就不是数据被大量积压的问题，而是大量的数据被直接搞丢了。

这种情况下，就不是说要增加consumer消费积压的消息，因为实际上没有啥积压的，而是丢了大量的消息，我们可以采取的一个方案就是，批量重导，这个之前线上也有遇到类似的场景，就是大量的消息积压的时候，然后就直接丢弃了数据，然后等高峰期过了之后，例如在晚上12点以后，就开始写程序，将丢失的那批数据，写个临时程序，一点点查询出来，然后重新 添加MQ里面，把白天丢的数据，全部补回来。

假设1万个订单积压在MQ里面，没有处理，其中1000个订单都丢了，你只能手动写程序把那1000个订单查询出来，然后手动发到MQ里面去再补一次。

#### 场景3：大量消息积压，导致MQ磁盘满了

如果走的方式是消息积压在MQ里，那么如果你很长时间都没有处理掉，此时导致MQ都快写满了，咋办？

这个时候，也是因为方案一执行的太慢了，只能写一个临时程序，接入数据来消费，然后消费一个丢弃一个，都不要了，快速消费掉所有的消息。然后走第二个方案，到凌晨的时候，在把消息填入MQ中进行消费。

### 如何设计一个消息中间件架构？

如果让你写一个消息队列，该如何进行架构设计？说下你的思路

这种问题，说白了，起码不求你看过那些技术的源码，但是你应该大概知道那些技术的基本原理，核心组成部分，基本架构个构成，然后参照一些开源技术把一个系统设计出来的思路说一下就好了。

**思路**

- 首先MQ得支持可伸缩性，那就需要快速扩容，就可以增加吞吐量和容量，可以设计一个分布式的系统，参考kafka的设计理念，broker - > topic -> partition，每个partition放一台机器，那就存一部分数据，如果现在资源不够了，可以给topic增加partition，然后做数据迁移，增加机器，不就可以存放更多的数据，提高更高的吞吐量。
- 其次得考虑一下这个MQ的数据要不要落地磁盘？也就是需不需要保证消息持久化，因为这样可以保证数据的不丢失，那落地盘的时候怎么落？顺序写，这样没有磁盘随机读写的寻址开销，磁盘顺序读的性能是很高的，这就是kafka的思路。
- 其次需要考虑MQ的可用性？这个可以具体到我们上面提到的消息队列保证高可用，提出了多副本 ，leader 和follower模式，当一个leader宕机的时候，马上选取一个follower作为新的leader对外提供服务。
- 需不需要支持数据0丢失？可以参考kafka零丢失方案

其实一个MQ肯定是很复杂的，问这个问题其实是一个开放性问题，主要是想看看有没有从架构的角度整体构思和设计的思维以及能力

## 分布式搜索引擎ES

业内目前来说事实上的一个标准，就是分布式搜索引擎一般大家都是用ElasticSearch，（原来的话使用的是Solr），但是确实，这两年大家一般都用更加易用的es。

ElasticSearch 和 Solr 底层都是基于Lucene，而Lucene的底层原理是 **倒排索引**

### 倒排索引

 在**搜索引擎**中每个文件都对应一个文件ID，文件内容被表示为一系列关键词的集合（实际上在搜索引擎索引库中，关键词也已经转换为关键词ID）。

如果是正向索引：

网页1中仅包含一句话：[厦门SEO](http://www.wangyuwen.com/)顾问潇湘驭文为您提供[厦门SEO培训](http://www.wangyuwen.com/)服务。

网页2中也仅包含一句话：SEO是一门艺术。

经过搜索引擎初步分词之后，网页1和2的正向索引如下图所示：

![什么是倒排索引与正向索引](http://www.wangyuwen.com/wp-content/uploads/2015/01/11.gif)

如果搜索SEO，搜索引擎就要检测所有的关键词，如果网页上关键词太多了，会造成大量的资源浪费。





倒排索引是相对正向索引而言的，你也可以将其理解为逆向索引。它是一种关键词与网页一一对应的数据结构。如下图所示：

![什么是倒排索引与正向索引](http://www.wangyuwen.com/wp-content/uploads/2015/01/21.gif)

从上图可以一目了然，倒排索引可以直接参与排名。

比如你搜索“SEO”，搜索引擎可以快速检索出包含“SEO”搜索词的网页1和网页2，为后续的相关度和权重计算奠定基础，从而大大加快了返回搜索结果的速度。

### ES的分布式架构原理能说一下么？

![01_elasticsearch分布式架构原理](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/2_%E5%88%86%E5%B8%83%E5%BC%8F%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/01_elasticsearch%E5%88%86%E5%B8%83%E5%BC%8F%E6%9E%B6%E6%9E%84%E5%8E%9F%E7%90%86.png)

elasticsearch设计的理念就是分布式搜索引擎，底层其实还是基于lucene的。

核心思想就是在多台机器上启动多个es进程实例，组成了一个es集群。

es中存储数据的基本单位是索引，比如说你现在要在es中存储一些订单数据，你就应该在es中创建一个索引，order_idx，所有的订单数据就都写到这个索引里面去，一个索引差不多就是相当于是mysql里的一张表。index -> type -> mapping -> document -> field。

index：mysql里的一张表

type：没法跟mysql里去对比，一个index里可以有多个type，每个type的字段都是差不多的，但是有一些略微的差别。

好比说，有一个index，是订单index，里面专门是放订单数据的。就好比说你在mysql中建表，有些订单是实物商品的订单，就好比说一件衣服，一双鞋子；有些订单是虚拟商品的订单，就好比说游戏点卡，话费充值。就两种订单大部分字段是一样的，但是少部分字段可能有略微的一些差别。

所以就会在订单index里，建两个type，一个是实物商品订单type，一个是虚拟商品订单type，这两个type大部分字段是一样的，少部分字段是不一样的。

很多情况下，一个index里可能就一个type，但是确实如果说是一个index里有多个type的情况，你可以认为index是一个类别的表，具体的每个type代表了具体的一个mysql中的表

每个type有一个mapping，如果你认为一个type是一个具体的一个表，index代表了多个type的同属于的一个类型，mapping就是这个type的表结构定义，你在mysql中创建一个表，肯定是要定义表结构的，里面有哪些字段，每个字段是什么类型。。。

mapping就代表了这个type的表结构的定义，定义了这个type中每个字段名称，字段是什么类型的，然后还有这个字段的各种配置

实际上你往index里的一个type里面写的一条数据，叫做一条document，一条document就代表了mysql中某个表里的一行给，每个document有多个field，每个field就代表了这个document中的一个字段的值

接着你搞一个索引，这个索引可以拆分成多个shard，每个shard存储部分数据。

接着就是这个shard的数据实际是有多个备份，就是说每个shard都有一个primary shard，负责写入数据，但是还有几个replica shard。primary shard写入数据之后，会将数据同步到其他几个replica shard上去。

通过这个replica的方案，每个shard的数据都有多个备份，如果某个机器宕机了，没关系啊，还有别的数据副本在别的机器上呢。高可用了吧。

es集群多个节点，会自动选举一个节点为master节点，这个master节点其实就是干一些管理的工作的，比如维护索引元数据拉，负责切换primary shard和replica shard身份拉，之类的。

要是master节点宕机了，那么会重新选举一个节点为master节点。

如果是非master节点宕机了，那么会由master节点，让那个宕机节点上的primary shard的身份转移到其他机器上的replica shard。急着你要是修复了那个宕机机器，重启了之后，master节点会控制将缺失的replica shard分配过去，同步后续修改的数据之类的，让集群恢复正常。

其实上述就是elasticsearch作为一个分布式搜索引擎最基本的一个架构设计



### ES查询和读取数据的工作原理是什么？

![01_es读写底层原理剖析](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/2_%E5%88%86%E5%B8%83%E5%BC%8F%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/01_es%E8%AF%BB%E5%86%99%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90.png)

（1）es写数据过程

1）客户端选择一个node发送请求过去，这个node就是coordinating node（协调节点）

2）coordinating node，对document进行路由，将请求转发给对应的node（有primary shard）

3）实际的node上的primary shard处理请求，然后将数据同步到replica node

4）coordinating node，如果发现primary node和所有replica node都搞定之后，就返回响应结果给客户端

（2）es读数据过程

查询，GET某一条数据，写入了某个document，这个document会自动给你分配一个全局唯一的id，doc id，同时也是根据doc id进行hash路由到对应的primary shard上面去。也可以手动指定doc id，比如用订单id，用户id。

你可以通过doc id来查询，会根据doc id进行hash，判断出来当时把doc id分配到了哪个shard上面去，从那个shard去查询

1）客户端发送请求到任意一个node，成为coordinate node

2）coordinate node对document进行路由，将请求转发到对应的node，此时会使用round-robin随机轮询算法，在primary shard以及其所有replica中随机选择一个，让读请求负载均衡

3）接收请求的node返回document给coordinate node

4）coordinate node返回document给客户端

（3）es搜索数据过程

es最强大的是做全文检索，就是比如你有三条数据

java真好玩儿啊

java好难学啊

j2ee特别牛

你根据java关键词来搜索，将包含java的document给搜索出来

es就会给你返回：java真好玩儿啊，java好难学啊

1）客户端发送请求到一个coordinate node

2）协调节点将搜索请求转发到所有的shard对应的primary shard或replica shard也可以

3）query phase：每个shard将自己的搜索结果（其实就是一些doc id），返回给协调节点，由协调节点进行数据的合并、排序、分页等操作，产出最终结果

4）fetch phase：接着由协调节点，根据doc id去各个节点上拉取实际的document数据，最终返回给客户端

（4）搜索的底层原理，倒排索引，画图说明传统数据库和倒排索引的区别

（5）写数据底层原理

1）先写入buffer，在buffer里的时候数据是搜索不到的；同时将数据写入translog日志文件

2）如果buffer快满了，或者到一定时间，就会将buffer数据refresh到一个新的segment file中，但是此时数据不是直接进入segment file的磁盘文件的，而是先进入os cache的。这个过程就是refresh。

每隔1秒钟，es将buffer中的数据写入一个新的segment file，每秒钟会产生一个新的磁盘文件，segment file，这个segment file中就存储最近1秒内buffer中写入的数据

但是如果buffer里面此时没有数据，那当然不会执行refresh操作咯，每秒创建换一个空的segment file，如果buffer里面有数据，默认1秒钟执行一次refresh操作，刷入一个新的segment file中

操作系统里面，磁盘文件其实都有一个东西，叫做os cache，操作系统缓存，就是说数据写入磁盘文件之前，会先进入os cache，先进入操作系统级别的一个内存缓存中去

只要buffer中的数据被refresh操作，刷入os cache中，就代表这个数据就可以被搜索到了

为什么叫es是准实时的？NRT，near real-time，准实时。默认是每隔1秒refresh一次的，所以es是准实时的，因为写入的数据1秒之后才能被看到。

可以通过es的restful api或者java api，手动执行一次refresh操作，就是手动将buffer中的数据刷入os cache中，让数据立马就可以被搜索到。

只要数据被输入os cache中，buffer就会被清空了，因为不需要保留buffer了，数据在translog里面已经持久化到磁盘去一份

3）只要数据进入os cache，此时就可以让这个segment file的数据对外提供搜索了

4）重复1~3步骤，新的数据不断进入buffer和translog，不断将buffer数据写入一个又一个新的segment file中去，每次refresh完buffer清空，translog保留。随着这个过程推进，translog会变得越来越大。当translog达到一定长度的时候，就会触发commit操作。

buffer中的数据，倒是好，每隔1秒就被刷到os cache中去，然后这个buffer就被清空了。所以说这个buffer的数据始终是可以保持住不会填满es进程的内存的。

每次一条数据写入buffer，同时会写入一条日志到translog日志文件中去，所以这个translog日志文件是不断变大的，当translog日志文件大到一定程度的时候，就会执行commit操作。

5）commit操作发生第一步，就是将buffer中现有数据refresh到os cache中去，清空buffer

6）将一个commit point写入磁盘文件，里面标识着这个commit point对应的所有segment file

7）强行将os cache中目前所有的数据都fsync到磁盘文件中去

translog日志文件的作用是什么？就是在你执行commit操作之前，数据要么是停留在buffer中，要么是停留在os cache中，无论是buffer还是os cache都是内存，一旦这台机器死了，内存中的数据就全丢了。

所以需要将数据对应的操作写入一个专门的日志文件，translog日志文件中，一旦此时机器宕机，再次重启的时候，es会自动读取translog日志文件中的数据，恢复到内存buffer和os cache中去。

commit操作：1、写commit point；2、将os cache数据fsync强刷到磁盘上去；3、清空translog日志文件

8）将现有的translog清空，然后再次重启启用一个translog，此时commit操作完成。默认每隔30分钟会自动执行一次commit，但是如果translog过大，也会触发commit。整个commit的过程，叫做flush操作。我们可以手动执行flush操作就是将所有os cache数据刷到磁盘文件中去。

不叫做commit操作，flush操作。es中的flush操作，就对应着commit的全过程。我们也可以通过es api，手动执行flush操作，手动将os cache中的数据fsync强刷到磁盘上去，记录一个commit point，清空translog日志文件。

9）translog其实也是先写入os cache的，默认每隔5秒刷一次到磁盘中去，所以默认情况下，可能有5秒的数据会仅仅停留在buffer或者translog文件的os cache中，如果此时机器挂了，会丢失5秒钟的数据。但是这样性能比较好，最多丢5秒的数据。也可以将translog设置成每次写操作必须是直接fsync到磁盘，但是性能会差很多。

实际上你在这里，如果面试官没有问你es丢数据的问题，你可以在这里给面试官炫一把，你说，其实es第一是准实时的，数据写入1秒后可以搜索到；可能会丢失数据的，你的数据有5秒的数据，停留在buffer、translog os cache、segment file os cache中，有5秒的数据不在磁盘上，此时如果宕机，会导致5秒的数据丢失。

如果你希望一定不能丢失数据的话，你可以设置个参数，官方文档，百度一下。每次写入一条数据，都是写入buffer，同时写入磁盘上的translog，但是这会导致写性能、写入吞吐量会下降一个数量级。本来一秒钟可以写2000条，现在你一秒钟只能写200条，都有可能。

10）如果是删除操作，commit的时候会生成一个.del文件，里面将某个doc标识为deleted状态，那么搜索的时候根据.del文件就知道这个doc被删除了

11）如果是更新操作，就是将原来的doc标识为deleted状态，然后新写入一条数据

12）buffer每次refresh一次，就会产生一个segment file，所以默认情况下是1秒钟一个segment file，segment file会越来越多，此时会定期执行merge

13）每次merge的时候，会将多个segment file合并成一个，同时这里会将标识为deleted的doc给物理删除掉，然后将新的segment file写入磁盘，这里会写一个commit point，标识所有新的segment file，然后打开segment file供搜索使用，同时删除旧的segment file。

es里的写流程，有4个底层的核心概念，refresh、flush、translog、merge

当segment file多到一定程度的时候，es就会自动触发merge操作，将多个segment file给merge成一个segment file。

### ES在数据量很大的情况下（数十亿级别）如何提高查询性能？

因为es没有想得那么好，第一次搜索得5-10s，后面反而快了，可能就几百毫秒。

#### （1）性能优化的杀手锏——filesystem cache

os cache，操作系统的缓存

你往es里写的数据，实际上都写到磁盘文件里去了，磁盘文件里的数据操作系统会自动将里面的数据缓存到os cache里面去

es的搜索引擎严重依赖于底层的filesystem cache，你如果给filesystem cache更多的内存，尽量让内存可以容纳所有的indx segment file索引数据文件，那么你搜索的时候就基本都是走内存的，性能会非常高。

性能差距可以有大，我们之前很多的测试和压测，如果走磁盘一般肯定上秒，搜索性能绝对是秒级别的，1秒，5秒，10秒。但是如果是走filesystem cache，是走纯内存的，那么一般来说性能比走磁盘要高一个数量级，基本上就是毫秒级的，从几毫秒到几百毫秒不等。

之前有个学员，一直在问我，说他的搜索性能，聚合性能，倒排索引，正排索引，磁盘文件，十几秒。。。。

学员的真实案例

比如说，你，es节点有3台机器，每台机器，看起来内存很多，64G，总内存，64 * 3 = 192g

每台机器给es jvm heap是32G，那么剩下来留给filesystem cache的就是每台机器才32g，总共集群里给filesystem cache的就是32 * 3 = 96g内存

我就问他，ok，那么就是你往es集群里写入的数据有多少数据量？

如果你此时，你整个，磁盘上索引数据文件，在3台机器上，一共占用了1T的磁盘容量，你的es数据量是1t，每台机器的数据量是300g

你觉得你的性能能好吗？filesystem cache的内存才100g，十分之一的数据可以放内存，其他的都在磁盘，然后你执行搜索操作，大部分操作都是走磁盘，性能肯定差

当时他们的情况就是这样子，es在测试，弄了3台机器，自己觉得还不错，64G内存的物理机。自以为可以容纳1T的数据量。

归根结底，你要让es性能要好，最佳的情况下，就是你的机器的内存，至少可以容纳你的总数据量的一半

比如说，你一共要在es中存储1T的数据，那么你的多台机器留个filesystem cache的内存加起来综合，至少要到512G，至少半数的情况下，搜索是走内存的，性能一般可以到几秒钟，2秒，3秒，5秒

如果最佳的情况下，我们自己的生产环境实践经验，所以说我们当时的策略，是仅仅在es中就存少量的数据，就是你要用来搜索的那些索引，内存留给filesystem cache的，就100G，那么你就控制在100gb以内，相当于是，你的数据几乎全部走内存来搜索，性能非常之高，一般可以在1秒以内

比如说你现在有一行数据

id name age ....30个字段

但是你现在搜索，只需要根据id name age三个字段来搜索

如果你傻乎乎的往es里写入一行数据所有的字段，就会导致说70%的数据是不用来搜索的，结果硬是占据了es机器上的filesystem cache的空间，单挑数据的数据量越大，就会导致filesystem cahce能缓存的数据就越少

仅仅只是写入es中要用来检索的少数几个字段就可以了，比如说，就写入es id name age三个字段就可以了，然后你可以把其他的字段数据存在mysql里面，我们一般是建议用es + hbase的这么一个架构。

hbase的特点是适用于海量数据的在线存储，就是对hbase可以写入海量数据，不要做复杂的搜索，就是做很简单的一些根据id或者范围进行查询的这么一个操作就可以了

从es中根据name和age去搜索，拿到的结果可能就20个doc id，然后根据doc id到hbase里去查询每个doc id对应的完整的数据，给查出来，再返回给前端。

你最好是写入es的数据小于等于，或者是略微大于es的filesystem cache的内存容量

然后你从es检索可能就花费20ms，然后再根据es返回的id去hbase里查询，查20条数据，可能也就耗费个30ms，可能你原来那么玩儿，1T数据都放es，会每次查询都是5~10秒，现在可能性能就会很高，每次查询就是50ms。

elastcisearch减少数据量仅仅放要用于搜索的几个关键字段即可，尽量写入es的数据量跟es机器的filesystem cache是差不多的就可以了；其他不用来检索的数据放hbase里，或者mysql。

所以之前有些学员也是问，我也是跟他们说，尽量在es里，就存储必须用来搜索的数据，比如说你现在有一份数据，有100个字段，其实用来搜索的只有10个字段，建议是将10个字段的数据，存入es，剩下90个字段的数据，可以放mysql，hadoop hbase，都可以

这样的话，es数据量很少，10个字段的数据，都可以放内存，就用来搜索，搜索出来一些id，通过id去mysql，hbase里面去查询明细的数据

#### （2）数据预热

假如说，哪怕是你就按照上述的方案去做了，es集群中每个机器写入的数据量还是超过了filesystem cache一倍，比如说你写入一台机器60g数据，结果filesystem cache就30g，还是有30g数据留在了磁盘上。

举个例子，就比如说，微博，你可以把一些大v，平时看的人很多的数据给提前你自己后台搞个系统，每隔一会儿，你自己的后台系统去搜索一下热数据，刷到filesystem cache里去，后面用户实际上来看这个热数据的时候，他们就是直接从内存里搜索了，很快。

电商，你可以将平时查看最多的一些商品，比如说iphone 8，热数据提前后台搞个程序，每隔1分钟自己主动访问一次，刷到filesystem cache里去。

对于那些你觉得比较热的，经常会有人访问的数据，最好做一个专门的缓存预热子系统，就是对热数据，每隔一段时间，你就提前访问一下，让数据进入filesystem cache里面去。这样期待下次别人访问的时候，一定性能会好一些。

#### （3）冷热分离

关于es性能优化，数据拆分，我之前说将大量不搜索的字段，拆分到别的存储中去，这个就是类似于后面我最后要讲的mysql分库分表的垂直拆分。

es可以做类似于mysql的水平拆分，就是说将大量的访问很少，频率很低的数据，单独写一个索引，然后将访问很频繁的热数据单独写一个索引

你最好是将冷数据写入一个索引中，然后热数据写入另外一个索引中，这样可以确保热数据在被预热之后，尽量都让他们留在filesystem os cache里，别让冷数据给冲刷掉。

你看，假设你有6台机器，2个索引，一个放冷数据，一个放热数据，每个索引3个shard

3台机器放热数据index；另外3台机器放冷数据index

然后这样的话，你大量的时候是在访问热数据index，热数据可能就占总数据量的10%，此时数据量很少，几乎全都保留在filesystem cache里面了，就可以确保热数据的访问性能是很高的。

但是对于冷数据而言，是在别的index里的，跟热数据index都不再相同的机器上，大家互相之间都没什么联系了。如果有人访问冷数据，可能大量数据是在磁盘上的，此时性能差点，就10%的人去访问冷数据；90%的人在访问热数据。

#### （4）document模型设计

有不少同学问我，mysql，有两张表

订单表：id order_code total_price

1 测试订单 5000

订单条目表：id order_id goods_id purchase_count price

1 1 1 2 2000

2 1 2 5 200

我在mysql里，都是select * from order join order_item on order.id=order_item.order_id where order.id=1

1 测试订单 5000 1 1 1 2 2000

1 测试订单 5000 2 1 2 5 200

在es里该怎么玩儿，es里面的复杂的关联查询，复杂的查询语法，尽量别用，一旦用了性能一般都不太好

设计es里的数据模型

写入es的时候，搞成两个索引，order索引，orderItem索引

order索引，里面就包含id order_code total_price

orderItem索引，里面写入进去的时候，就完成join操作，id order_code total_price id order_id goods_id purchase_count price

写入es的java系统里，就完成关联，将关联好的数据直接写入es中，搜索的时候，就不需要利用es的搜索语法去完成join来搜索了

document模型设计是非常重要的，很多操作，不要在搜索的时候才想去执行各种复杂的乱七八糟的操作。es能支持的操作就是那么多，不要考虑用es做一些它不好操作的事情。如果真的有那种操作，尽量在document模型设计的时候，写入的时候就完成。另外对于一些太复杂的操作，比如join，nested，parent-child搜索都要尽量避免，性能都很差的。

很多同学在问我，很多复杂的乱七八糟的一些操作，如何执行

两个思路，在搜索/查询的时候，要执行一些业务强相关的特别复杂的操作：

1）在写入数据的时候，就设计好模型，加几个字段，把处理好的数据写入加的字段里面

2）自己用java程序封装，es能做的，用es来做，搜索出来的数据，在java程序里面去做，比如说我们，基于es，用java封装一些特别复杂的操作

#### （5）分页性能优化

es的分页是较坑的，为啥呢？举个例子吧，假如你每页是10条数据，你现在要查询第100页，实际上是会把每个shard上存储的前1000条数据都查到一个协调节点上，如果你有个5个shard，那么就有5000条数据，接着协调节点对这5000条数据进行一些合并、处理，再获取到最终第100页的10条数据。

分布式的，你要查第100页的10条数据，你是不可能说从5个shard，每个shard就查2条数据？最后到协调节点合并成10条数据？你必须得从每个shard都查1000条数据过来，然后根据你的需求进行排序、筛选等等操作，最后再次分页，拿到里面第100页的数据。

你翻页的时候，翻的越深，每个shard返回的数据就越多，而且协调节点处理的时间越长。非常坑爹。所以用es做分页的时候，你会发现越翻到后面，就越是慢。

我们之前也是遇到过这个问题，用es作分页，前几页就几十毫秒，翻到10页之后，几十页的时候，基本上就要5~10秒才能查出来一页数据了

1）不允许深度分页/默认深度分页性能很惨

你系统不允许他翻那么深的页，pm，默认翻的越深，性能就越差

2）类似于app里的推荐商品不断下拉出来一页一页的

类似于微博中，下拉刷微博，刷出来一页一页的，你可以用scroll api，自己百度

scroll会一次性给你生成所有数据的一个快照，然后每次翻页就是通过游标移动，获取下一页下一页这样子，性能会比上面说的那种分页性能也高很多很多

针对这个问题，你可以考虑用scroll来进行处理，scroll的原理实际上是保留一个数据快照，然后在一定时间内，你如果不断的滑动往后翻页的时候，类似于你现在在浏览微博，不断往下刷新翻页。那么就用scroll不断通过游标获取下一页数据，这个性能是很高的，比es实际翻页要好的多的多。

但是唯一的一点就是，这个适合于那种类似微博下拉翻页的，不能随意跳到任何一页的场景。同时这个scroll是要保留一段时间内的数据快照的，你需要确保用户不会持续不断翻页翻几个小时。

无论多少页，性能基本上都是毫秒级的

因为scroll api是只能一页一页往后翻的，是不能说，先进入第10页，然后去120页，回到58页，不能随意乱跳页。所以现在很多产品，都是不允许你随意翻页的，app，也有一些网站，做的就是你只能往下拉，一页一页的翻

![img](data:image/png;base64,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%0A1+jRo1VcXKwmTZrI09Oz3H1ffPFFbdy4UfHx8bLZbLJYLv8+JDs7W+vXr5ck%0A2Ww2bd++XevWrdOYMWPk7s4fLQAAAABUN5KYE8XHx2vDhg06evSoJOnnn39W%0As2bNZLVaNXv2bLVr165Kx1m0aJECAwMvWx4XF+d43adPHxmGoRMnTujpp582%0A5wIAAAAAAFeFkO1EgwYNkiSNHj1aeXl5mjRpkiTp22+/1eeffy5PT0/169dP%0Aubm5Kioq0nPPPacPPvhA2dnZys3NVVxcnAIDAzVy5Ei5ublVeq633npLRUVF%0AGj58uLMvCwAAAABQAUK2E61cuVKHDh3S9OnTlZGRoT59+ujhhx/WRx99pOzs%0AbK1Zs0ahoaF67733lJOTo4EDB2rgwIGSzt/DvX79esXHx2vBggWOnuyZM2dq%0AwoQJkqSXXnrJca7WrVtrx44d1X+RAAAAAAAHJj5zksLCQs2dO1dTpkzRxIkT%0AVadOHbVs2VJPPfWUMjMz1b59e6Wmpko6Pxla8+bNq3TctWvXOl5PmTLFKbUD%0AAAAAAK4NPdlO4uHhodtuu02jR4/W7NmzNWnSJPXo0UPt27eXv7+/li1bpk2b%0ANqlDhw76/vvvHfdRFxcX65dfflFBQYEeeeQRZWZmatiwYTIMQ5J09uxZDRgw%0AwHGeyZMnKzo62iXXCAAAAAAoi5DtJIZhaN68eXr77bd15MgRbd26VfPmzZMk%0ALVy4UD169NCQIUPUuHFjNWrUSOHh4SouLlZ8fLxatWold3d3TZs2TU888YQ+%0A+ugjxz3ZcXFxWrVqlSsvDQAAAABQAUK2Ey1atEhDhw7VypUr1bRpU+3atUsJ%0ACQkKCAiQxWJRly5dNGvWLL3xxhuSJHd3d61evVrS+XuyIyIiVFhYKDc3Nz3w%0AwAOSzvdkl74ODw937AsAAAAAcD1CthPl5OQoKipKCQkJuvfee/W3v/1NCQkJ%0AslgsstlsysnJkXT+/u3yZGZmKjQ0VJJ04sQJxzOxpfPPyH700UedfxEAAAAA%0AgCojZDtRo0aNNGXKFM2ZM0ezZ8+Wl5eX9uzZo6ioKE2dOlXZ2dmaPXu2Jk6c%0AqDNnzig+Pr7M/j/++GOVJ0SzWq2O17GxsY7XSUlJ5lwMAAAAAOCKCNlOkpOT%0Ao1mzZikpKUl33XWXVq1apdOnT2vLli0aNWqUvL29NXfuXPn4+Oj111/XuHHj%0AHEPIi4qK5OnpqTVr1uiuu+6SVHaYuCSVlJSUOR9hGgAAAABcj5DtJH5+furZ%0As6cmTJggLy8vSVJwcLCaN2+uqKgoRUdHO2YMv/POO7V8+XIFBQVp+PDhysjI%0A0IMPPqji4mLFxcVJkvz9/bV8+XLH8RkuDgAAAAA1j+HqAmqamJgYu1Qzeobt%0AdrsjiNcEpUPSk5OTa05RcIno6OhthmF0dHUdAADc7Ox2+4aUlJQerq4DwH9Y%0AXF0AKlaTAjZwMQI2AAA1g2EY3V1dA4CyGC4O4JrVhBEfcI4ZM2ZoxYoVmjBh%0AggYNGmTqsUtHxfD3BwCuz8UT3wKoOQjZFeCHFgAAAADgajFc/BJ2u32Dq2uo%0AyWgfAAAAAKgYPdmXYOIIAAAAAMC1oicbAAAAAACTELIBAAAAADAJIRsAAAAA%0AAJMQsgEAAAAAMAkhGwAAAAAAkxCyAQAAAAAwCSEbAAAAAACTELIBAAAAADAJ%0AIRsAAAAAAJMQsgEAAAAAMAkhGwAAAAAAk7i7uoCaJjo6epthGB1dXUdNZbfb%0AN6SkpPRwdR0AcLNLTU1Vs2bNZLHwfTkAADUJv5kvQcCunGEY3V1dAwDc7Hbt%0A2qURI0Zo586dV9w2Li6uGioCAACl6MmuQFJSkqtLqHGsVqurSwCAG5bNZtP2%0A7du1bt06jRkzRu7u5f+KPnz4sP70pz/phRdeUNu2bSVJWVlZSktLu+LPaZvN%0Apt/+9reXLd+6davjdUlJiTp37qzIyEhJ0sGDB7V161bdfffdqlevnmO7Q4cO%0A6dtvv73q6wSc7ZdfftHs2bO1fft2eXp6auLEierVq9dl2z3xxBNKSkrSpk2b%0A5OPj44JKAdyoCNkAANQAffr0kWEYOnHihJ5++ulytzl06JBGjRql0aNHq0+f%0APo7l2dnZeuGFF/TSSy+VG6JL2e12FRYWOr5ILikpUceOlw/gCg0N1ZIlSyRJ%0Affv2lSS5ubk5lklSv379rv4iASdLS0vTE088oUceeUR/+ctflJOTo4KCgsu2%0A+/LLL3Xw4MHqLxDATYGQ7ULnzp2Tl5eXq8sAANQAb731loqKijR8+PBy1ycn%0AJ+vFF1/Uc889V6ZXzm63q0mTJnrxxRc1btw4vfbaa+rcubNee+01bdmyRb/+%0A+qsGDBggSVq5cmWVajl+/LiGDBki6XyAl84H8tJlknTq1KlruUzAqebNm6cu%0AXbo4/h2V10Odn5+vOXPmaPjw4Zo9e3Y1VwjgZsA92U6UlZWl9PR0SdJdd90l%0A6fxENSdOnFBeXp66d++uwsLCcvfdvHlztdUJAHC91q1bV7hu8+bNevLJJ5WX%0Al6e3335bffv2VY8ePdS1a1d1795dAwcO1JtvvqkmTZpo/Pjx+vHHHzV27FhH%0AgFi1apVWrVpVpToMw1B0dLSWLFmiJUuWqFevXvroo4/UqVMnjRkzRo0aNdKr%0Ar76qrl27mnLdgFlKSkq0YcMG3XPPPZVuN3/+fHXo0MFxu8XFsrOz9dxzzyk2%0ANlb9+/fXwoULZbVaK/y8BgDloSfbiTZt2qS1a9fq3XfflXS+t2H69OkaPXq0%0AiouL1aRJE3l6epa774svvqiNGzcqPj5eNput3Nljs7OztX79eklVv5cPAFD7%0AREVFaeLEibr11lvl7+8vHx8f+fr6qk6dOpdt+8477+jvf/+77rjjDn311VeS%0ApCVLlqhfv35Vuu900KBBkqT4+HjHsnXr1qlBgwbauXOnTpw4oWHDhik4OFhP%0APPGEEhMTTbpK4PqkpaWpoKBAWVlZGjBggI4dO6bo6GhNnjxZ9evXl3R+ToOP%0AP/5Yy5cvV0ZGxmXHSEhIUH5+vj755BPZ7XaNHTu2ui8DwA2AJOZE8fHx2rBh%0Ag44ePSpJ+vnnn9WsWTNZrVbNnj1b7dq1q9JxFi1apMDAwMuWXzxjbFXu5QMA%0A1E6BgYGOId9X8thjj6mwsFBFRUX67rvvFBAQoJKSEg0bNkzz58+Xm5tbpfuX%0ADinPzs7WV199pX/84x+aO3eu5s2bp759+6pv377y9fW97msCzJabmytJ+vHH%0AH/XBBx+oqKhI48aN08svv6y//vWvkqTXX39dI0aMUL169S4L2adPn9bmzZu1%0AePFihYSESJIef/xxPlcBuGqEbCcq7Q0YPXq08vLyNGnSJEnSt99+q88//1ye%0Anp7q16+fcnNzVVRUpOeee04ffPCBsrOzlZubq7i4OAUGBmrkyJFX/FB0pXv5%0AAAC1W2FhoWJjY+Xn51fhNjk5Odq6dat8fX21dOlSdezYUQcPHtTDDz+sbt26%0AyTCMSvffvXu3/va3v+mXX37RmTNn1Lt3b/3P//yP6tevr3nz5umjjz7S008/%0Arfz8fIWEhKhu3bp66aWXKhyVBVQnb29vSdKTTz6pgIAASdLw4cP13//937LZ%0AbNq0aZMyMjI0ePDgcvfPzMyUJDVt2tSxzN/f38lVA7gREbKdaOXKlTp06JCm%0AT5+ujIwM9enTRw8//LA++ugjZWdna82aNQoNDdV7772nnJwcDRw4UAMHDpR0%0A/h7u9evXKz4+XgsWLHD0ZM+cOVMTJkyQJL300kuOc7Vu3Vo7duyo/osEAFSr%0A9evXl/vF66UzhR84cECjRo3S8uXLJUlNmjTRd999pyZNmpR73I0bN6pJkybq%0A2bOnvvnmGzVu3FjJyclKTk7WkiVL9OyzzzomOysoKNB3332nhQsXErBRYzRu%0A3Fg+Pj7Kyclx9EQbhqE6derIYrFo9erVyszMVM+ePSWdv9VOOj+D/iuvvKKI%0AiAhJ0rFjxxxfRmVlZbngSgDUdkx85iSFhYWaO3eupkyZookTJ6pOnTpq2bKl%0AnnrqKWVmZqp9+/ZKTU2VdH4ytObNm1fpuGvXrnW8njJlilNqBwDUfmPHjlXd%0AunXLLPvqq68qfJb2v/71Lx04cEC9evVSQECAli5dqiVLljhmF9+/f79jMrSV%0AK1cqODhYt99+u9OvA6gqd3d33XPPPfrLX/6i7OxsHT9+XPPnz3c8hm727Nna%0AvHmzNm7cqI0bN2revHmSpC+++EKxsbFq3Lixmjdvrrlz5+rMmTNKT0/X4sWL%0AXXlJAGoperKdxMPDQ7fddptGjx6t2bNna9KkSerRo4fat28vf39/LVu2TJs2%0AbVKHDh30/fffO+73KS4u1i+//KKCggI98sgjyszM1LBhw2QYhiTp7NmzZe7L%0Amzx5sqKjo11yjQAA81wcfmNjYx2vS59pfbUu7e3ev3+/PvvsM/3jH/9wLDMM%0AQ4ZhKCcnR6mpqRo2bNg1nQuoKcaMGaPXXntN/fv3l5ubm373u9/p2WefrfL+%0Ar776qhISEnT33XerZcuWio+P186dO5lQFsBV4SeGkxiGoXnz5untt9/WkSNH%0AtHXrVsc3pgsXLlSPHj00ZMgQNW7cWI0aNVJ4eLiKi4sVHx+vVq1ayd3dXdOm%0ATdMTTzyhjz76yPFhKS4ursqPYQEA1B5VDdN9+vS56mOfPXtWY8aM0aBBg8rc%0Ab2qxWNS5c2d1795dbdu2VcuWLSWdfwZ26TOxL34eNs/JRk3n6empiRMnauLE%0AiVfctm3btpf9u2vWrFmZ3utPPvlEYWFh5T7lBQAqQsh2okWLFmno0KFauXKl%0AmjZtql27dikhIUEBAQGyWCzq0qWLZs2apTfeeEPS+WFOq1evlnT+nuyIiAgV%0AFhbKzc1NDzzwgKTzH5RKX4eHhzv2BQDc2AzD0B133KH58+eX+4HfZrPpscce%0Ac4x8KjVu3Dj5+/vrz3/+c7nPtn7rrbcuW7ZixQrH/amlMzAvWLBAjRo1cmyT%0AlpZ2XdcD1ETr169Xq1at1KBBA+3atUuJiYlVntkfAEoRsp0oJydHUVFRSkhI%0A0L333qu//e1vSkhIkMVikc1mU05OjqTz92+XJzMzU6GhoZKkEydOOJ6JLZ1/%0AtMqjjz7q/IsAANQIHh4eWrhwYYXrLRZLuet/97vfSTr/5W1VlQZsSY5gfXHA%0AvnQb4EZx6NAhvfbaazp9+rRCQkLUr18/PfbYY64uC0AtQ8h2okaNGmnKlCma%0AM2eOZs+eLS8vL+3Zs0dRUVGaOnWqsrOzNXv2bE2cOFFnzpxRfHx8mf1//PHH%0AKk+IZva9fAAAADebESNGaMSIEa4uA0AtR8h2kpycHM2aNUtJSUm66667tGrV%0AKp0+fVpbtmzRqFGj5O3trblz58rHx0evv/66xo0b5xhCXlRUJE9PT61Zs8bR%0A83DxMHHp/KNaLkaYBgAAAADXI2Q7iZ+fn3r27KkJEybIy8tLkhQcHKzmzZsr%0AKipK0dHRjvvm7rzzTi1fvlxBQUEaPny4MjIy9OCDD6q4uFhxcXGSJH9/f8ez%0ATiWGiwMAAABATUTIdqJu3bqVuzwmJuayZWFhYZJU5tEqdrvdEcQvvh9bkgID%0AA7Vy5UqzSgUAAAAAmIDnEdRgl84QCwAAAACo2QjZAAAAAACYhOHiFbh4tm4A%0AAAAAAKqCnuxL2O32Da6uoSajfQAAAACgYvRkXyIlJaWHq2sAAAAAANRO9GQD%0AAAAAAGASQjYAAAAAACYhZAMAAAAAYBJCNgAAAAAAJiFkAwAAAABgEkI2AAAA%0AAAAmIWQDAAAAAGASQjYAAAAAACYhZAMAAAAAYBLD1QUAqH1iYmLskpSUlOTq%0AUmq8YcOGaefOna4uAwBqHavVqsTERFeXUaNZrVZJUnJyMp/pgRqEnmwAcCIC%0ANgBcG77IBVBbubu6AAC4GdS2D4szZszQihUrNGHCBA0aNMjUY5f2vNS2NgFQ%0AfUp/TgBAbURPNgAAAAAAJiFkAwAAAABgEkI2AAAAAAAmIWQDAAAAAGASQjYA%0AAAAAACYhZAMAAKfZsmWLzpw543hvs9k0derUCt9fKi8vz6n1AQBgNkI2AADX%0AYMeOHbJaraaHwKysLN17772y2WymHtcZqtIGv/zyi5566imdPXtWkmS32/Xx%0Axx871l/6ftu2bXrllVc0ZswY9e/fX0899ZQkqX379urXr1+Z/zp27OikK6u9%0A/vKXv6hXr17q2LGjHnroIe3YscOx7vDhwxo7dqx69Oih2NhYPfXUU0pPT3dh%0AtQBwYyJkAwDgIocPH9bAgQNVWFjoWBYWFqaPP/5YFsuN8St6+PDhatSokTZs%0A2KC4uDj17t1bkhQXF3fZe5vNpnr16qlTp0764YcftGLFCi1evFiS5OXlpU8/%0A/bTMf25ubi67rpqqXbt2WrlypdatW6fWrVvrz3/+s+x2uyRp48aNat++vVat%0AWqXPPvtMnp6emjhxoosrBoAbj7urCwAA4Gb166+/6vDhw64uw+lmzZolwzDU%0Av39/lZSUqGPHjlq/fr0kXfa+RYsWatGihaZOnSpPT09Xll0r9erVy/G6b9++%0A+vTTT2W322UYhh566KEyX9489NBDGj16tGw22w3zpQ4A1AT8RAUAoAKFhYV6%0A7bXX1KNHD3Xp0kUvvPCCcnJyyt32u+++09ChQ9WpUyf1799fW7dudaxbunSp%0A7r77bsXGxur11193LB8+fLgkKTY2VlarVdLlQ7BtNpsWLVqke+65R506ddLv%0Af/977dq1q9LjXqnuimotPffq1asVFxfnOGZlNUjS9u3bNXToUN155526//77%0AywxRliTDMByvS3tVKzJr1iwNGDBAv/76q+Lj4xUfHy9JOnfu3GXDxUtKSio9%0A1s3KbrcrMzNTS5Ys0f333+8I0JcG6VOnTikoKMixPDs7W88995xiY2PVv39/%0ALVy4UFartcxICwDAldGTDQBABaZNm6a0tDQtXbpUderU0YQJEzR79mwlJCRc%0Atm1ubq4mTpyoFi1aaO7cuZo5c6ZWr16ttLQ0zZo1S++8846ioqJ04MABxz6L%0AFi3S8OHDtXXr1gp7bd944w1t3LhRU6dO1e23367Dhw/L29u70uNeqe6Kai21%0Abds2ffLJJ45AXFENJ06ckCQtX75cb775pry8vDRp0iRNmzZNy5Yt04IFC7R4%0A8WIVFhY6gvyVek2ff/55de7cWc8884xWrlzpWD5jxgzdfffdZbZds2ZNhce5%0AWW3btk2jRo2SJHXt2lXPPPNMudsVFxfrf//3f3Xfffc5liUkJCg/P9/xZz92%0A7NhqqRkAbjT0ZAMAUI7Tp0/r888/1/jx4xUWFqbAwEA9/PDDWrduXbnb9+jR%0AQ5GRkUpNTZWfn5/S09NVXFwsDw8PGYahzMxM+fj46Pbbb69yDWfPntWyZcv0%0A0ksv6Y477pC7u7siIyPVsGHDCsPE6C0AACAASURBVI9blborqrXUsGHD5Ovr%0AKz8/v0prKDVmzBjVr19fAQEBGjJkiFJTU2Wz2TRy5Eht3LhR0vkJ3Xr37q0/%0A/OEPstls6t27t3r37q0+ffpIkuO9JP3rX/+SxWLRzJkzdfLkSQ0YMEB//etf%0ANWDAAFmtVg0YMEADBgzQO++8Q9C+RKdOnfTvf/9bH374oU6dOqWXX3653O1m%0Azpwpi8WiRx99VNL5vzebN2/WmDFjFBISotDQUD3++OPVWToA3DDoyQYAoByZ%0AmZmy2+0aMmTIZeuKioouWzZ37lytXr1a7dq1U506dSSd77UNCwvT1KlTNWfO%0AHP3973/XhAkTFB0dXaUa0tPTVVJSolatWl22rqLjXqluDw+PCmstFRERUaUa%0ASoWGhjpe+/r6ym63q7i4uEzvfFhYmNauXatNmzZpwYIFWrRokaT/3JO9du1a%0ASeeHMKempsrf318dOnTQpEmT9PHHHzuGnFutVq1cuZJJzyphsVgUGRmpJ598%0AUs8++6ymTp1aZvTAG2+8oZSUFM2fP9/xZ5SZmSlJatq0qWM7f3//6i0cAG4Q%0AhGwAAMoRHBwsSfrss8/UoEGDSrdNS0vTokWL9OGHHyoyMlJbt27Vl19+6Vjf%0At29f9erVS2+99ZbGjh3rCJRXEhQUJOn8LOTl9YCXd9wr1X2lWqWy91BfqYar%0AtWbNGnXv3r3C9YmJierbt68WLFigXr16KS4uTuPHj9fu3bsd25Tepy1Jq1at%0Auu6abmTu7u5lAvbbb7+tzZs3KzEx0fF3RZL8/PwkSceOHXO8zsrKqt5iAeAG%0AQcgGADhNVlaWFixYUO66GTNmlHk/cuRIhYWFVUdZVRIWFqaYmBi9/vrrev75%0A5xUSEqLU1FRlZ2df9nzm0qHWR48eVUhIiJYsWeJYd/ToUWVlZSkqKkqNGzdW%0AYWGhY7bngIAASdIPP/yg1q1bO95fXEO3bt00ffp0TZ48Wc2bN9e+ffvk5+cn%0Ai8VS7nGvVHdltVbUDhXVcLW++uor7dixQy+88EKF2wQEBGjgwIGOvzcWi0Wv%0AvvqqYz092RXbv3+/9u7dqx49euj06dOaP3++Ywi+JL377rvauHGj3nvvvTIB%0AW5IaN26s5s2ba+7cuXr55Zd19uxZx+PTAABXh5ANAHCa0NBQff311zp16tRl%0A61asWOF4HRwcrPHjx1dnaVXy6quv6pVXXtGgQYNUVFSkyMhIjRkz5rLtbrnl%0AFg0ePFjPP/+86tevr8GDB2vLli2Szg+HnjJlitLT0xUeHq5p06Y5eoqbNm2q%0A+Ph4jRkzRn5+fuX2cE+fPl1z5szRqFGjlJubq1tuuUXTp0+Xp6dnhcetrO7K%0Aaq1IRTVcSU5OjmbOnKni4mJ9+OGH+te//qU5c+bI19e3wn3++Mc/lulJR9V5%0Ae3tr8eLFSkhIkI+Pj3r16qVnn33WsT4xMVGSygRvSY6J91599VUlJCTo7rvv%0AVsuWLRUfH6+dO3fK3Z2PiwBwNfgtBuCqxcTE2CUpKSnJ1aXUeKWPZaptbTVj%0AxgytWLFCEyZM0KBBg67rWDNnztQ///nPSrcZNGiQJkyYcF3nQc2zZMkSJScn%0A649//KMWLFigpKQk2e12ubm5yd3dXe7u7iouLlZxcbEKCwvVtGlTvffee7JY%0ALIqLi9P69eu1fft2TZ482XHMQ4cOlblvWFKZWchhnk8++UTvvPOOPv/882o/%0Ad2392VndStspOTmZz/RADcJXkwAAp4qLi7tiyO7Zs2c1VYPqdN999+nBBx+U%0AxWIp0/NdUlKioqIix9B1wzBkGIY8PDwc9w936dJFktSuXTtCdDVZv369WrVq%0ApQYNGmjXrl1KTEzUgAEDXF0WANQ6hGwAgFNZrVYFBATozJkz5a6vW7euozcG%0AN5aKnv3t5uZ2xXuqp0yZ4oySUIlDhw7ptdde0+nTpxUSEqJ+/frpsccec3VZ%0AAFDrELIBAE7l7u6u7t27a/Xq1eWu7969O5NYATXAiBEjNGLECFeXAQC1nuXK%0AmwAAcH0qGw7OUHEAAHAjIWQDAJyuY8eO5c4o7evrqw4dOrigIgAAAOcgZAMA%0AnM7T01Ndu3a9bHm3bt0qvG8XAACgNiJkAwCqRXnDwhkqDgAAbjSEbABAtejc%0AubO8vLwc7728vBQbG+vCigAAAMxHyAYAVAsvLy/99re/dbzv0qVLmdANAABw%0AIyBkAwCqTVxcXLmvAQAAbhSEbABAtbl48rPyJkIDAACo7dxdXQAA4Obh6+ur%0Arl27yjAM+fj4uLocAAAA0xGyAQDVqmfPnjIMw9VlAAAAOAUhGwBuEsOGDdPO%0AnTuvap+ZM2dq5syZTqknISHBKce9GlarVYmJiaYc61ra90ZnZvteiva+nDPb%0AGwBQddyTDQA3CQLJ5ZKSkkw7Fu17OTPb91K09+Wc2d4AgKqjJxsAbjJ8ED/P%0AarU65bi073nOat9L0d7nVVd7AwCujJ5sAAAAAABMQsgGAAAAAMAkhGwAAAAA%0AAExCyAYAAAAAwCSEbAAAAAAATELIBgAAAADAJIRsAAAAAABMQsgGAAAAAMAk%0AhGwAAAAAAExCyAYAAAAAwCSEbAAAAAAATELIBgAAAADAJIRsAIApduzYIavV%0Aqry8PKefo7Cw0GnnqKlo3+pFewMArhUhGwAAAAAAkxCyAQAAAAAwCSEbAHDV%0Ali5dqrvvvluxsbF6/fXXy6zbvn27hg4dqjvvvFP333+/duzY4Vj33XffaejQ%0AoerUqZP69++vrVu3SvrPsNnVq1crLi7OccwzZ87o+eefV+fOndWvXz9t27at%0A+i7ShWjf6kV7AwDM5O7qAgAAtUtaWppmzZqld955R1FRUTpw4ECZ9cuXL9eb%0Ab74pLy8vTZo0SdOmTdOyZcskSbm5uZo4caJatGihuXPnaubMmVq9erVj323b%0AtumTTz6R3W6XJCUkJCg3N9exzbhx46rpKl2H9q1etDcAwGz0ZAMAroqHh4cM%0Aw1BmZqZ8fHx0++23l1k/ZswY1a9fXwEBARoyZIhSU1Nls9kkST169FBkZKRS%0AU1Pl5+en9PR0FRcXO/YdNmyYfH195efnp1OnTumbb77Rn/70J4WEhCgkJESP%0APfZYtV6rK9C+1Yv2BgCYjZANALgqYWFhmjp1qubNm6cHHnhAKSkpZdaHhoY6%0AXvv6+sputzuCx9y5c9W/f3/Nnz9fhw4dkiRHYJGkiIgIx+usrCxJUpMmTRzL%0A/Pz8zL+gGob2rV60NwDAbIRsAMBV69u3rz755BN16tRJY8eOrdI+aWlpWrRo%0Akd59913Nnj1b/fv3v2wbwzAcr0sDyLFjxxzLSoPKjY72rV60NwDATIRsAMBV%0AOXr0qH744QcZhqHGjRursLDQcc9pZUp7/44ePaozZ85oyZIllW7fuHFjRUZG%0Aau7cuTpz5ozS09O1ePFiU66hJqN9qxftDQAwGxOfAQCuSklJiaZMmaL09HSF%0Ah4dr2rRpZXrsKnLLLbdo8ODBev7551W/fn0NHjxYW7ZsqXSfV155RZMnT1bv%0A3r3VsmVLDRo0SDt37jTrUmok2rd60d4AALNd+bcIAFwiJibGLklJSUmuLqXG%0As1qtkmpGW9WkWmoCs9uD9i3L2e1Be5d1o7XHjXY9zlLaTsnJyXymB2oQhosD%0AAAAAAGASQjYAAAAAACYhZAMAAAAAYBJCNgAAAAAAJiFkAwAAAABgEkI2AAAA%0AAAAmIWQDAAAAAGASQjYAAAAAACYhZAMAAAAAYBJCNgAAAAAAJiFkAwAAAABg%0AEkI2AAAAAAAmIWQDAAAAAGASd1cXAACoXlar1dUl3NBo3+pFewMAahp6sgHg%0AJkEYuZyZbUL7Xs6ZbUJ7X442AYCagZ5sALhJJCYmXvO+ycnJMgxD0dHRJlZk%0AjtJgkZSU5NI6rqd9cfVobwBATUXIBgBc0bp16ySpRoZsAACAmoTh4gCAStls%0ANq1fv15ff/21bDabq8sBAACo0QjZAIBK7dy5U8eOHVNWVpZ+/vlnV5cDAABQ%0AoxGyAQCVKh0qfulrAAAAXI6QDQCokN1u1/r16x3v161bJ7vd7sKKAAAAajZC%0ANgCgQnv37lV6errjfXp6uvbu3evCigAAAGo2QjYAoELlDQ+/uGcbAAAAZRGy%0AAQAVKi9kc182AABAxQjZAIBy7d+/XwcPHrxs+YEDB3TgwIHqLwgAAKAWIGQD%0AAMpV2bBwerMBAADKR8gGAJSrsiDNfdkAAADlI2QDAC6TlpZW6Szie/bsKTPr%0AOAAAAM5zd3UBAHAzsFqtri7BdPfcc4+rSyjjRmxjAABQ+9CTDQBORPADgGvD%0Az08AtRU92QDgRImJia4u4aodPXpU/fr1q9K2n376qRo2bOjkiipX+kE8KSnJ%0ApXUAAABIhGwAwCUaNmx4WWAlyAIAAFQNw8UBAAAAADAJIRsAAAAAAJMQsgEA%0AAAAAMAkhGwAAAAAAkxCyAQAAAAAwCSEbAAAAAACTELIBAAAAADAJIRsAAAAA%0AAJMQsgEAAAAAMAkhGwAAAAAAkxCyAQAAAAAwCSEbAAAAAACTELIBAAAAADAJ%0AIRsAAAAAAJMQsgEAAAAAMIm7qwsAANQeM2bMqNbz3XbbbRo4cGCVtrVarU6u%0Axvluv/12LV682NVlAACA60DIBgBcUZ06dVRQUKAVK1ZU+7mvFLKtVquSkpKq%0AqRrn2rlzp6tLAAAA14mQDQC4ovfff187duyo1nPOnDmzStslJiY6uZLqcSP0%0AxAMAAEI2AKAKWrVqpVatWlXrOasasgEAAGoSJj4DAAAAAMAkhGwAAAAAAExC%0AyAYAAAAAwCSEbAAAAAAATELIBgAAAADAJIRsAAAAAABMQsgGAAAAAMAkhGwA%0AAAAAAExCyAYAAAAAwCSEbAAAAAAATELIBgAAAADAJIRsAAAAAABMQsgGAAAA%0AAMAkhGwAAAAAAEzi7uoCAAC42WRlZWnBggXlrpsxY0aZ9yNHjlRYWFh1lAUA%0AAExAyAYAoJqFhobq66+/1qlTpy5bt2LFCsfr4OBgjR8/vjpLAwAA14nh4gAA%0AVDOLxaK4uLgrbhcXFyeLhV/VAADUJvzmBgDABaoSsnv27FkNlQAAADMRsgEA%0AcAGr1aqAgIAK19etW1dWq7UaKwIAAGYgZAMA4ALu7u7q3r17heu7d+8uNze3%0A6isIAACYgpANAICLVDYcnKHiAADUToRsAABcpGPHjvL19b1sua+vrzp06OCC%0AigAAwPUiZONmYfH29m7s6iIA4GKenp7q2rXrZcu7desmT09PF1QEAACuFyHb%0A+byjoqIOSwqRpMjIyBXe3t6xzjxhw4YNX5Tk58xzXA9vb++IC23iX7rM09Oz%0AdatWrbZUtE+bNm1+up5z+vv739m8efPNknyu5zhmCAwMjPfy8oq8dHnTpk0X%0A+vn5daviYTyCgoIekhRsbnUAqlt5w8IZKg4AQO3l7uoCbnSBgYG9i4qKjkg6%0A4e3tHe7r6xubn5+fXMkuvtHR0SdtNtu5ijawWCxeKSkpdSUVXLazr2+74ODg%0AR48ePTrjCqVZ/P39OwcGBt535MiRsZKKKtqwRYsWX+Tk5KzPzMycVYX93MLD%0Aw18LCQkZIck4efJkYlpa2nhJ9tINAgMDh+fm5n4r6WzpsuDg4AEXllWqTZs2%0AP9lstmJJslgsjr+/derUaVFQULAvMzPzjVOnTq1s27btjkv39fDwaNSuXbuD%0Adru98NJ1O3bsiLjSuSsTExNjKygo2F/eujp16kQmJyc7vtDy9PS8JSwsbNye%0APXs6SyopXX727Nkv6tWr90hOTs43Vzpfw4YNX2rYsOELPj4+r6anp79wPbUD%0AcK3OnTvLy8tL586d/7Hv5eWl2FinfhcLAACciJDtZMHBwY+cOHEiUZICAwOf%0A8vDwCIuOjv710u1Onjz5weHDhx+XZBiGUefHH3/0quiYMTExdkmGJHl7e995%0A6623rild5+bm5m+z2fLuuOOO0xXt/+OPPwa2bds2XZLdw8Oj4ZEjRyaogpBd%0Ap06dln5+fl337ds3VJKutF+DBg3+XLdu3d/v3bv3NyUlJX6tW7fekJeXt/fU%0AqVMLJLm1a9cuy83NLcButxe2a9fuhCRt3749JCgo6CEvL69bQ0JC/lh6rF27%0AdsUUFBTsvbSm3bt3/0Y6H7gPHTr0p9zc3G/btGnz759//jmqdJvU1NSOeXl5%0AGaXvw8PDXy8uLj6SlZU155LDNZCUVVFbVZXdbi/cuXNni/LWRUdHl/nC5Nix%0AY28GBgbeHxoaOqpBgwbjLt2+bdu2aaWvT548+UFGRsaEi9cHBQX1Dw0NfeqX%0AX37p2qxZs4/Pnj37zZkzZ9ZcehwAtYOXl5d++9vfat26dZKkLl26yMurwl8B%0AAACghiNkO5GPj0+junXr9t+/f/9DkgLr1as3LDk5OVAX9eBKUkRExJzi4uJT%0A13KO/Pz8//vxxx8DJcnPz69bRETEW7t37+6kcnq5L5aamvoHu93uedttt22t%0AbLuwsLCnT5069b+STldlv5CQkFFHjx6dlJ+ff1iSTp48+V5ISMiwCyHbcHd3%0Ar5ecnOx9YXO3mJiYHD8/v242my0nJSXF8anyjjvuOGUYRnFYWNh/16tX73EP%0AD49mrVu3/kGSSv8vSeHh4S/s3bv3Hl340uGiOhJsNtu5tLS0Mf7+/v28vb3b%0A7Nu3b3xYWNgLnp6e4UeOHBnt7e3dqUWLFh8fOXLkyezs7NWVtcOVGIbhWdGQ%0AdsMwLr2x0nbo0KH/KigoOHH8+PG55e0THh4+Iz09/Q1Jxy9eHhAQ0LdJkyYf%0AHDhw4MGzZ89+e/Dgwf9q1qzZsgMHDjx45syZL6/nGgC4TlxcnCNkx8XFubga%0AAABwPQjZThQcHDzOMAwPSeciIiJeOXXq1BJdCNjh4eHT09PTX5RkBAUFxe/Z%0As+fu6zmXl5fXLc2aNVtusVi82rZtm1reNjt27GiqC8OT8/Lykr29ve+8wmH9%0Ag4KCHtm1a1fn0gVX2K+Bp6dnk5ycnP8rXZCbm/t9SEjIqEu2K+3ZdZek4ODg%0A/8rIyLh0eLu7YRgFWVlZbxQVFaU3aNDgpd27d/+mTZs2P13ck20Yhq+kHMMw%0AyswvcPjw4TGtWrVa7+fn16N58+bLzp0790vr1q2/c3d3r7dv377unp6erVu2%0AbPlpWlra/7vegC2d78m+uCf9Ypf2ZEtSQUHBvgsvAyIiIqampaW9IClXOv9l%0ASXBw8Ij09PSpF+8TGhr6THh4+PSDBw8+XBqoz5w58+WhQ4ceiYyM/GdmZubr%0AmZmZ03TREHQAtcPFk5+VNxEaAACoPQjZTuLl5RVZr169R0rf5+fn/3Dy5MlP%0ASt/Xr19/3IWQXe/MmTNfFxYW7rrWc9WpU6dF8+bNv/Dw8AhLTk42ytvm4iHm%0AVRUaGjo8Nzf3+8LCwp1V2d7Hx6ehJBUUFBwrXVZSUnLCzc0tUJJbRfsdPnz4%0AqYiIiNlubm5u2dnZqyTJMAz3/Pz8AkkKCAi418PDIzwsLGzCpfsahmGXZLHb%0A7fZLVp3bs2dPnI+PT9s9e/bc6uvr+4ewsLBn09PT/5/dbvcoLCxM379//wM5%0AOTlfV+XarsQwDM/bb799X0XrSl83bNjwxfr16z9/4b56L0nnLBaL92233bb5%0A4MGD/fPz8zPCw8NnZmZmTpeUL0leXl7NIiIi5nl7e7fbs2dPz/z8/O8khcTE%0AxBxPTk4Ozc7OXrV37967mjVrtqJevXoPHz169IVTp059aMZ1Aagevr6+6tq1%0AqwzDkI+Py+dnBICaxEvnOxAqnD+oEp4X9rv0cyKu4MJ8R6mi7a4JIdtJGjVq%0A9Obx48ffatCgwSRJOnny5CJvb+9O3t7evU6dOrWsdLsGDRo8eujQoT9fuv8d%0Ad9yRXZXz+Pv7d23WrNlHGRkZE5o0aZIYFRV10KRLMOrXr///0tPTx17FPqVB%0A2nbRMpvO/+N0hPzSe7EvUnLs2LHEVq1afVVQUJCcn59/5MIIgAJvb+9wd3f3%0AwKKiokxPT8/GhmG4XzxcXJJHVFTUPjc3t+A2bdr8dOLEifePHTs2W1JYZGTk%0A2xaLJTg7O3tpUFDQkJ07d3YNCgr6XYsWLT5LS0sbFRER8U52dvaqC192VDjR%0AXFXs27fvD2fOnPmivHUBAQF9S18fPXp0+tGjR6df1LtdePjw4Sfq16//x+bN%0Am2/59ddfPy0pKTl3/PjxeaX7hIaGPl1SUnJyx44dbSWVe1tBXl5e8s6dO6Ma%0ANWo0STV4ZnkAFevZs6cM46q+CwWAWqVZs2ZLjx8/Pi0nJ8dxi12LFi2+3Ldv%0A3+8q2CW4Xbt2u1NTU3vl5uZuv8Lhg3T+dsm80gVhYWGjvLy82h06dOjRSvbz%0A9fb2bpufn/9/ktS0adP5p06dev/s2bNb/Pz87nJzc/P89ddf1168g4+PT4e8%0AvLzvdSGA+vr69pKUlZub65h4NygoqP/p06fX6jo/Y17CIygo6IHTp09/oQo+%0AE0pSgwYNJmVmZpaOiLRIqi8ps1GjRq9kZGSMr8qJbr/99l8u3OJZbv3e3t4d%0A8/PzUyWdvGRVnd/85jcnf/jhhyp9Hg0KChp8+vTpFT4+PqElJSXeF4J9rUfI%0AdpKcnJwNx44de7s0ZEtSw4YNx+fm5m64eDt3d/fwiIiI59LS0konwLIXFxef%0A3L59e0g5h7VIsl0IqaXfKpVkZWX95cSJE+81adIk8aeffrqlvHou9GRXWUBA%0AwO8keWZnZ39yxY1LCykpOS1JXl5ewefOnTsjSW5ubvWKi4tP6HzYtkjnJzq7%0AsIt7TExMkSQVFhb+fPz48XcaNmz41v79++8zDMNdUkFwcPCzp0+fXhoWFjb2%0AyJEjo0qHi3t7e9/p5ubmmZOT801ISMgTgYGB9+7bt+/3F87ftGXLlpszMzNf%0AzcvL29SiRYuNRUVFGbfddttXNpst12az5QUGBg67EEont2jR4rN9+/Zd9fNy%0AfH1920VGRn5e3joPD4/woqKi9EuXVzSL+bFjx97x9vZuHxoaOiojIyNBF31R%0AceTIkT+r7BcXFcmt6g9O/MewYcO0c2eVBmvARaxWq6tLqFYJCQmuLgG4KVit%0AViUmJrq6jJtGnTp1Wvn7+/c6cODA8EaNGr184fOO/P39K/wMdsstt7zt5uYW%0AHBkZ+a/Kjp2dnf3PoqKiY35+fl327dvXXxfmJqpbt+6grKysNyvbt169evfX%0Aq1dv2N69e+Mu7DPg5MmTb0qSzWY727Rp02V169b96vDhw8/qwijDiIiIV3Ny%0AcraVTk7r7e3dolGjRstSU1N75ubm/uDt7d04PDz87dOnT3eQiSG7qk+XqV+/%0A/pjMzMypzZo1W+rl5dXq3LlzOw8cOPBESEjIYwEBAXEWi8XHZrPl7969u4Mk%0AVdRJFxUVtUeX9GT/9NNPzSWV1KtXb6ifn99vd+/e3aN169abS9cbhmFYLBbf%0A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%0APVWv1786Njb2ztDQ0DXY+cdC2Wy231EUxaitrf13X1/fGaFQ6L9TTSoQCEop%0AiuKx2eyKVCoVyx1vqijkAERKpXJRd3f38YUqp+mXJTo0NHR59gdciJxACBM+%0A2RKJ5DS9Xr82nU6PcTiccofDsaxA1+TWrVuNABwymew8vV7/wo4dO04BMJZM%0AJkebmpoGMmZIewWxWHy83+9/a6p6gUBwVFVV1V/HxsY+sFqtv+bxeNUajWZJ%0AaWnpvRRFJTK+82mMH+qxGAwGx+l0PhQKhT6x2+33lJaW/kGr1d5XVlb2MEVR%0AKYqiSIqi4gwGg81gMPgZc6XPzWbzgr21JxoaGhoaGhqa/Qmfz9cXFxffAAAC%0AgaCSIIhjAaR7enrmkSTZ39ramszNLgMgjnEtZoQgiFaSJLcA4+/LQqFwvkaj%0AudnpdN6TaU+wWKxw7nxyufxSn8/39/7+/pMB+Lhcbj0AJp/P18disbDb7X4w%0AE7QsH7ZUKj3bbDafP91+VCrVNVqtdjmbzVYWeC9morCbn1wgEAh3da1yoSiK%0As4fZZcQznctuty8GAIVC8bOioqJb+/v7z4lEItskEskZpaWl94dCoY1Wq/Ub%0AAEm73b7Y5XI95XK5Xpo7d25fOBzekm8aPg0pj8fzosFgWN/d3X0UMqboGo3m%0ArtHR0TeEQmFdZWXlO6Ojo8/iEBawAVrI3q9wudyjKisrH6YoihweHv5Dfn00%0AGrU7HI57R0ZGnphuHLvdfms8Hh8KhUJbpmsnFArPKi4uvjmdTpMOh+MuzMzs%0AIpT1Yd4HJLu7u4/N/d7Z2VkDAIFA4N/bt28/mc1mCyiK8kSjUUtuR7fb/Xjm%0AowMA/H7/P+Px+I8ikUgbAPT29p6wq8lnG61QKBQe73a7n5qqPhqNdlsslusC%0AgcAHmfF7h4aGLstpwsD4wzabw5vC+CkeCQB2u/3OTGRzZNpwMP6bzH1gx6eY%0APpXZD50Tm4aGhoaGhuagpaio6FaPx/OMWq2+LRqNbhoYGNgkk8mSBd7LsibC%0AlEaj+UNRUdF1bDZblk6nY8XFxbfnjfkrAOBwOEU1NTXfUBSV7ujo0AgEgtKK%0AiornSZI8IRwOfwMAYrH4KIqi4mKx+MexWOxxl8v1WKF1yuXyM9hstqq6uvof%0A+RaVHA5HY7Vab8paS3o8ntVZ6848c3E2RVFUblkikXBEIpFOlUp1BWbByMjI%0A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%0A69atkl00C5AkGdhFmxAwbkIOAPF4HFu2bFEhR6OaQ2RkZOTxkZGRxwvU5ZJA%0AgYBgexlfniJqEoODgwtzvnK3bt1aibx4SWNjY3/L93d2uVzd2c8dHR3aqcaP%0AxWJmq9V6Xc5YEwJ2jtk//H7/er/fv36qcUKh0CdtbW0sZFK2TdWuEGaz+Wez%0AaX+goIXs/UBnZ2cdj8erLS8vf5bL5VaOjo6+5nQ6V6rV6qvYbLaqp6enNBKJ%0ADGs0mj+wWCyJx+N5Lmve0tLS4m9vb1cZjcaerq6u/0NGk11eXv6MxWL5FQDo%0A9fpXcudrbm725AqlDAaD397eXjDUfyGi0ehXfX19P8x+5/P5olQqtZNptUAg%0AaMz9QeWTG8o/O392PwDg8Xj+XF1d/ZrL5SIASLRa7cNDQ0M/LTCUKJ1OT3ro%0AEQQhA8Dk8/mVsVjMnFtXX1//PwAwmUxRAOju7j42x0Q8H75Op3tILpcvZDKZ%0AvEAg8J7ZbL4WGVP5oqKiGzUazWImkykbHR1dbbPZ/tja2mrt7+8/LxAIbMgu%0Ap6WlxTEwMHBBIBD4sECf32TWtSl3XTnWBFOuQSAQHFNfX79paGjo51qtdgUA%0Aymw2XyYSiZrVavVdFEUlbDbbVT6f772p7gMNDQ0NzaHDI488gg0bNiAQCKCm%0Apga33347mpqaAAAWiwVPPvkkvv76a8RiMbS0tGDJkiUoLS09wKumoTksKCRg%0AH8ocNIqmKdhb2v2DEjpo1L6Hp9Vq79fr9c9bLJar0+l0NBaLtdfV1W3kcrnl%0AoVDo3xwOpxEABAKBMRqNzsjmUyaTTZziZCItTsBms5UdHR1l2T82m63ckw2w%0A2ezKRCJhL1TX0NCwbaq/jDn3Tmg0mtuMRmOP0Wj8hsfj1RiNxjaj0fgfAJRe%0Ar38z438+cSjA5XIVbDZbpdFolmbLKIoqA4DcHH5Zuru7jwWAtrY2wVRm8Vn0%0Aev0agUDQumPHjpYtW7ZUsFisIr1evwoAeDxelU6ne8xsNl+6devWYq/X+yoA%0At9/vf0uhUFyaHUOpVP40a4ozRZ9p1zXdGrLw+fzGjo6OmmAw+FFVVdVfeDxe%0AbXt7u35sbOwdrVb7CGho9iOxGO3tQEOzr2hubsb69evx8ccfo66uDrfeeiso%0AalzR88knn+CII47AO++8gw8++ABcLhd33XXXAV4xDc0hARfj1o8zYVeachqa%0A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%0AzpzpkBqsVqt+97vf6de//nWFgC1JY8eOVe/evfXBBx9o1apVOnz4sMaMGWM/%0AdsKECcrJyVFJSYnmzJkjqXxwPnPmjPbu3av8/HxFRERo2rRp9km3+vXrV2Vd%0APj4+ioiI0MKFCxUTE1NpMHWm8PBw/etf/1J6erok6ejRo3r00UdlNpu1atUq%0A9ezZs1bn2bhxY6Wfc0hIiH152LBhMhgMysrK0qxZsxxzAwCAWiFkAwBQj127%0Adk3nzp2rcb+srCxZrVYNGjRI7u7ucnd3d1gN33zzjQwGgwYOHFjlPt999536%0A9OkjSXryyScVGxurMWPGaN26dWrdurW+/fZbbdmypcJwcZvNZg/HzZs316xZ%0As/T73/9ea9askZ+fX4219enTR82aNVNiYqJCQ0Pv4y7v3+jRoyVJM2fOVGFh%0AoRYuXChJ2rNnj3bs2CEPDw8999xzun79uoqLixUREaG//OUvys3N1fXr1xUS%0AEiJfX1+9/PLLcnNzq/Zaa9asUXFxsSZNmvSgbwsAcAeeyQaAOuadd95RaGio%0AnnrqKb344ov6/vvv7W1Wq1WrV69WcHCwgoKCtGbNGtlstlq3o7yYmBiFhYWp%0AX79+WrlypX17UVGRli9fruDgYA0YMEDz589XQUGBvT03N1cRERHq16+ffvaz%0An2nDhg0ym80qKirS999/L7PZrPj4eIWFhWno0KHav3+//vrXvyo4OFhDhgzR%0AN998U6trlZ1r3759Gj9+vPr27asxY8YoNTXVfnxZiOrXr1+1r1R66aWXyu1X%0Adu7CwsIK+9Z0/3f68ssvNXTo0Go/6/379+vpp58ut96hQwd5enrKZrNpx44d%0A+vnPf64zZ87IarUqJSVF4eHhGjVqlIqKihQeHq7w8HBFRkbq5s2bmjp1qsLD%0Aw1VcXGxvq0pYWJh27dpVbX3OEBcXp9WrV8vPz09ubm4aNmyYNm/erNOnTys3%0AN1ebNm3Stm3bNH78eI0aNUojR45UXFycEhMT1bRpU/tQ8PXr1ys2NlaxsbHq%0A1auXfXnAgAH2az322GOuuk0AaPQI2QBQx/Ts2VNxcXFKSEjQY489pt/+9rf2%0AoLxp0ybt3r1bH3/8sTZs2KDPP/9cn332mf3YmtrxPxcuXNCKFSsUFRWlhIQE%0ADR8+3N4WFRWlY8eOKSYmRtu2bVNOTo5WrVplb4+MjFR+fr7i4+O1YcMGJSUl%0AVTj/iRMn9Omnn6pv375asGCBzp49q+3btysoKEjvvPNOra8llYazNWvWaOfO%0AnQoICNDSpUvtbRs3bpQk7d27t9p3Odd2v9rWdLsjR46oR48eVbbfvHlT+/bt%0A0+zZs+3DxDdu3KixY8fqzTff1JdffqnU1FSZzWa9+uqrKi4uVrdu3RQXF6cJ%0AEyYoLCxMcXFx+tWvfqVly5YpLi7O/sfd3d2+XJVu3brpyJEj1d7zg1ZUVKS1%0Aa9dqyZIlevPNN9WkSRN16dJF06dPV0ZGhn784x/r5MmTkkonQ+vUqVOtznv7%0AlwdLlix5ILUDAO4OIRsA6pjQ0FD5+PioefPmGj58uHJzc+0he+vWrZo8ebIC%0AAgLUsWNHjRw5Utu3b7cfW1M7/sfd3V0Gg0EZGRny9vZWt27dJEk5OTnasWOH%0A5s2bJ39/f/n6+mrChAlKSEiwt+/evVtz5syRn5+fWrVqpVdeeaXC+V944QV5%0Ae3vrmWeeUXZ2tiZNmiRvb28NHTpU58+fl8ViqfFaZV599VX5+fnJx8dHY8eO%0AVVpaWq1ed3UvalvT7S5fvqzWrVtX2Z6YmKiCggLFxsbq1q1bSkhIUGpqqlJT%0AU3Xp0iUdPXpUHTt21Lp16xQcHKwmTZpIKn1d1+bNm/Xiiy9KKh06Pnv2bMXH%0Ax6uwsFB5eXm1es66devWysrKustPwrHc3d31+OOP68MPP9SWLVu0cOFCBQcH%0A67333lNERIRCQkKUlJQki8Wib7/91j4qoaSkRKmpqbp165ZeeuklZWRkaOLE%0AiRoxYoRGjBih/Px8+/KIESP03//+16X3CQDgmWwAqJNsNpsyMzP18ccf6/nn%0An5fRaNTVq1eVkZFRrsfwiSee0NatWyWpxnapdJjz0qVLtWfPHvn5+WnkyJF6%0A7733tHfv3monzWqI/P39tXTpUv3xj3/U5s2b9cYbb6hXr17KyMiQzWbTuHHj%0AKhxTXFysjIwMSVKHDh3s25s3b15h37KJqcpm6y57frgsQFoslhqvVaZly5bl%0ArmWz2VRSUvJA/pvVVFNlz3LbbDYZDIYqz5mcnKxWrVrZ1zt37qzx48dr586d%0AmjBhgiTJ09NTn3zyidatWyepNFzOnz9fY8aM0RNPPCGp9LVXHTt21M6dOxUT%0AE6O//e1vevbZZ+/rfp3FYDDo/fff17vvvqvz589r7969ev/99yVJGzZsUHBw%0AsMaNG6d27dopMDBQbdq0UUlJicLDw9W1a1eZTCZFRUVp6tSp+uSTT+zPZIeE%0AhDBaBQDqGEI2ANQx+/bt04wZMyRJAwcO1OzZsyXJ3hPXokUL+76+vr7Kz8+X%0A1Wqtsd1oNCoyMlI3btxQfHy8bDab5s6d66zbqpOGDx+u0NBQrVmzRnPnztWu%0AXbvsn9/27dsVEBBQ4ZhmzZpJKu29LVvOzMy8p+vXdC1XuJea/Pz8dPXqVQUG%0ABlbaPnfuXI0aNcq+3qFDB4WHh+uf//ynnn32Wbm7u+vUqVM6ceKE/csLk8mk%0AGTNmaPr06fah4JcuXbJfo2z50KFDGjJkSLXPXF+5cqVWk6Q9aBs3btT48eMV%0AFxenDh06KDU1VZGRkfLx8ZHRaNSAAQO0YsUKrV69WlLpZ/D5559LKv2CoW3b%0AtioqKpKbm5t92H1+fr59uU2bNvZjAQCuw3BxAKhj+vTpowMHDmjr1q3Kzs7W%0A4sWLJZX2fEoqNzzWaDTaexBraq/tMOfGIj09XYcOHZLBYFC7du1UVFQkm80m%0Af39/9e7dWytXrlRmZqYsFovS0tK0f/9+SVK7du3UqVMnrV27Vnl5ebp48aI2%0Abdp0TzXUdK3a8PHxkSQdOnRIeXl591TH/db0+OOPKyUlpcr2st7720VHR+v5%0A559XVFSUpNL5BMaPH6/XX3/dvk+nTp1UVFRkn9jLy8ur0uWioqJq7yklJcXe%0AG+5KBQUF6t69uyIjI/Xpp59q/vz5mj9/voxGo6xWq31yuaruJyMjwz4iICsr%0AS7GxsTpw4IBiY2MVHR2ts2fPOu1eAABVI2QDQB1kNBrVsWNHTZs2TV999ZWs%0AVqs9TF27ds2+X25urnx9fWU0Gmtsr+0w58bCYrFoyZIl6t+/v2JiYhQVFWX/%0AQuLtt9+W0WjU6NGj1b9/fy1atKjcLO1vv/22rl69qrCwMM2bN08jR46UVNrz%0AeLdqulZNynqF58yZU663+H7cbU1DhgzRzp07azzvhAkTdPnyZR0/flxJSUkK%0ACQnRpUuXtGXLFu3Zs0cpKSk6dOhQjROz3a1du3a5/PVdkhQYGKglS5aoW7du%0AWrVqlQoKCnT8+HGVlJRo8eLFys3N1apVq7Ro0aJKJ3I7fPhwrSdEM5vNFWae%0Ar272eQCA4zBcHADqOJPJJKPRqMDAQDVv3lxHjx61D5k9evSounfvLkk1tjty%0AmHND0LZt2ypnpG7RooWWL19e5bGPPvpoud7r+Ph4+fv7y2g0qkePHuVCYk3r%0A1V3rzn2r2rZgwQItWLCgynprquNuaqpMaGiooqOjlZycXGWQM5vN9lEZcXFx%0A8vf317vvvqvAwEAlJSXplVdeUWBgoKZMmaK///3vys7O1ocffqjCwkL7cOhr%0A165Vuly2z9q1a+Xv71/uugcPHlR2drbLQ3ZBQYFWrFih5ORkDRo0SJ999ply%0AcnL0n//8RzNmzJCXl5fWrl0rb29vrVy5Uq+//rp9CHlxcbE8PDz0xRdfaNCg%0AQZLKDxOX/jeSpYyjv6gAANQeIRsA6pBTp04pLS1NwcHBysnJ0bp16zRkyBBJ%0Apb3bZe9k/tGPfqS8vDzFxcXZX9tTU/vtw5wXL16s/Pz8ex7m3NglJiaqa9eu%0ACggIUGpqqqKjozVixAhXl+UyJpNJixcvVlRUlD766CP7ZG+3KwvYksq917qk%0ApEQFBQX2ieJKSkpkMBg0ZMgQ+9/9e1VYWKg//OEPWrRokX2iMFdp1qyZBg8e%0ArDfeeEOenp6SSr/M6NSpk7p3765evXrZR1L07dtXsbGxevjhhzVp0iRdunRJ%0AL7zwgkpKShQSEiKpdBRKbGys/fy5ubn65S9/6fwbAwBUUPVUoABQhd69e9uk%0AhtFTUtbrVlfuJT09XRERETp58qS8vb0VGhqq3/zmN/Ly8pJU+kqjZcuWKSEh%0AQc2bN9eUKVM0evRo+/E1tZ8+fVqRkZFKS0tTly5dFB4erqioKB04cKBWr0JC%0AqY8++khbtmxRTk6O/Pz89Nxzz2nq1KkuD3KuVlhYKA8Pj3saNv8glJSUqKio%0AqNLQX1t16WdETbO4O1td+mzuVn2u/XZl93Hw4MG68xcDACEbwN0jZDcc8fHx%0A+tOf/qQdO3a4uhSgTmrsPyOqU58/m/pc++0I2UDdRLcFADQiiYmJunjxoiwW%0Ai44cOdLohzkDAAA4Wt0YzwUAcIqzZ89q+fLl5YY5T5kyxdVlAXUeM3MDAGqL%0AkA0AjcjkyZM1efJkV5cB1Btms7neDyl+kPjyAQAqImQDAABUITo62tUlAADq%0AGZ7JBgAAAADAQQjZAAAAAAA4CCEbAAAAAAAHIWQDAAAAAOAghGwAAAAAAByE%0AkA0AAAAAgIMQsgEAAAAAcBBCNgAAAAAADkLIBgAAAADAQUyuLgAA6gKz2ezq%0AEgAAANAA0JMNoFEjXANA48TPfwAPCj3ZABq16OhoV5cAAACABoSebAAAAAAA%0AHISQDQAAAACAgxCyAQAAAABwEEI2AAAAAAAOQsgGAAAAAMBBCNkAAAAAADgI%0AIRsAAAAAAAchZAMAAAAA4CCEbAAAAAAAHISQDQAAAACAgxCyAQAAAABwEEI2%0AAAAAAAAOQsgGAAAAAMBBCNkAAAAAADgIIRsAAAAAAAcxuboAAPWX2Wx2dQkA%0AAABAnUJPNoC7ZrPZ/uXqGgAAgGSz2fa7ugYAAAAAAAAAAAAAAAAAAAAAAAAA%0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA%0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAqKP+H023Ehi3pQLZAAAAAElFTkSu%0AQmCC)



###  ES生产集群的部署架构是什么？每个索引的数据量大概是多少？么给索引大概有多少分片？

这个问题，包括后面的redis什么的，谈到es、redis、mysql分库分表等等技术，面试必问！就是你生产环境咋部署的？说白了，这个问题没啥技术含量，就是看你有没有在真正的生产环境里干过这事儿！

有些同学可能是没在生产环境中干过的，没实际去拿线上机器部署过es集群，也没实际玩儿过，也没往es集群里面导入过几千万甚至是几亿的数据量，可能你就不太清楚这里面的一些生产项目中的细节

如果你是自己就玩儿过demo，没碰过真实的es集群，那你可能此时会懵，但是别懵。。。你一定要云淡风轻的回答出来这个问题，表示你确实干过这事儿

3、面试题剖析

其实这个问题没啥，如果你确实干过es，那你肯定了解你们生产es集群的实际情况，部署了几台机器？有多少个索引？每个索引有多大数据量？每个索引给了多少个分片？你肯定知道！

但是如果你确实没干过，也别虚，我给你说一个基本的版本，你到时候就简单说一下就好了

（1）es生产集群我们部署了5台机器，每台机器是6核64G的，集群总内存是320G

（2）我们es集群的日增量数据大概是2000万条，每天日增量数据大概是500MB，每月增量数据大概是6亿，15G。目前系统已经运行了几个月，现在es集群里数据总量大概是100G左右。

（3）目前线上有5个索引（这个结合你们自己业务来，看看自己有哪些数据可以放es的），每个索引的数据量大概是20G，所以这个数据量之内，我们每个索引分配的是8个shard，比默认的5个shard多了3个shard。



## 分布式缓存

在项目中缓存是如何使用的？缓存如果使用不当会造成什么后果？

###  为啥在项目里要用缓存？

用缓存，主要是两个用途：高性能 和 高并发

####  **高性能**

假设有这么个场景，有一个操作，一个请求过来，然后执行N条SQL语句，然后半天才查询出一个结果，耗时600ms，但是这个结果可能接下来几个小时就不会变了，或者变了也可以不用立即反馈给用户，这个时候就可以使用缓存了。

我们可以把花费了600ms查询出来的数据，丢进缓存中，一个key对应一个value，下次再有人来查询的时候，就不走mysql了，而是直接从缓存中读取，通过key直接查询出value，耗时2ms，性能提升300倍。这就是所谓的高性能。

就是把一些复杂操作耗时查询出来的结果，如果确定后面不怎么变化了，但是马上还有很多读请求，这个时候，就可以直接把结果存放在缓存中，后面直接读取缓存即可。

![image-20200421122211630](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/3_%E5%88%86%E5%B8%83%E5%BC%8F%E7%BC%93%E5%AD%98/images/image-20200421122211630.png)

就第一次从数据库中获取，后面直接从缓存中获取即可，性能提升很高

####  **高并发**

MySQL这么重的数据库，并不适合于高并发，虽然可以使用，但是天然支持的就不好，因为MySQL的单机撑到2000QPS的时候，就容易报警了

![image-20200421124116765](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/3_%E5%88%86%E5%B8%83%E5%BC%8F%E7%BC%93%E5%AD%98/images/image-20200421124116765.png)

#### 为什么缓存可以支持高并发

首先因为缓存是走内存的，内存天然就可以支持高并发，但是数据库因为是存储在硬盘上的，因此不要超过2000QPS

#### 场景

所以要是有一个系统，高峰期过来每秒的请求有1W个，要是MySQL单机的话，一定会宕机的，这个时候就只能用上缓存，把很多数据放到缓存中，这样请求过来了之后，就直接从缓存中获取数据，而不查询数据库。缓存的功能很简单，说白了就是一个 key - value式数据库，单机支撑的并发量轻松超过一秒几万 到 十多万，单机的承载量是mysql单机的几十倍。

### 缓存带来的不良后果

场景的缓存问题有三个

- 缓存与数据库双写不一致的问题
- 缓存穿透
- 缓存雪崩
- 缓存并发竞争



## Redis

###  面试题

- [Redis和Memcache有什么区别](https://gitee.com/moxi159753/LearningNotes/tree/master/校招面试/面试扫盲学习/4_Redis的面试连环炮##Redis和Memcache有什么区别)
- [Redis的线程模型是什么？](https://gitee.com/moxi159753/LearningNotes/tree/master/校招面试/面试扫盲学习/4_Redis的面试连环炮##Redis的线程模型是什么？)
- Redis的数据类型及应用场景？
- 为什么单线程的Redis比多线程的Memcache的效率要高？
- 为什么Redis是单线程但是还可以支撑高并发？
- Redis如何通过读写分离来承受百万的QPS
- Redis的持久化策略有哪些？AOF和RDB各有什么优缺点
- Redis的过期策略以及LRU算法
- 如何保证Redis的高并发和高可用？
- redis的主从复制原理能介绍一下么？
- redis的哨兵原理能介绍一下么？
- Redis主备切换的数据丢失问题：异步复制、集群脑裂
- Redis哨兵的底层原理

Redis最基本的一个内部原理和特点就是NIO异步的单线程工作模型。Memcache是早些年个大互联网公司常用的缓存方案，但是现在近几年都是使用的redis，没有什么公司使用Memcache了。

注意：Redis中单个Value的大小最大为512MB，redis的key和string类型value限制均为512MB

### Redis和Memcache有什么区别

从Redis作者给出的几个比较

- Redis拥有更多的数据结构
  - Redis相比Memcache来说，拥有更多的数据结构和支持更丰富的数据操作，通常在Memcache里，你需要将数据拿到客户端来进行类似的修改，在set进去。这就大大增加了网络IO的次数和体积，在Redis中，这些复杂的操作通常和一般的set/get一样高效。所以，如果需要缓存能够支持更复杂的结构和操作，那么Redis是不错的选择
- Redis内存利用率对比
  - 使用简单的key-value存储的话，Memcache的内存利用率更高，而Redis采用Hash结构来做key-value存储，由于其组合式的压缩，其内存利用率会高于Memcache
- 性能对比
  - 由于Redis只使用了单核，而Memcache可以使用多核，所以平均每核上Redis在存储小数据比Memcache性能更高，而在100K以上的数据中，Memcache性能更高，虽然Redis最近也在存储大数据的性能上进行优化，但是比起Memcache还有略有逊色。
- 集群模式
  - Memcache没有原生的集群模式，需要依赖客户端来实现往集群中分片写入数据，但是Redis目前是原生支持cluster模式的。

### Redis都有哪些数据类型，及使用场景

- String

  - 最基本的类型，就和普通的set 和 get，做简单的key - value 存储

- Hash

  - 这个是 类似于Map的一种结构，就是一半可以将结构化数据，比如对象（前提是这个对象没有嵌套其它对象）给缓存在redis中，每次读写redis缓存的时候，可以操作hash里面的某个字段

  ```
  key=150
  value={
    "id": 150,
    "name": "张三",
    "age": 20,  
  }
  ```

  - Hash类的数据结构，主要用来存放一些对象，把一些简单的对象给缓存起来，后续操作的时候，你可以直接仅仅修改这个对象中某个字段的值。

- List

  - 有序列表，可以通过list存储一些列表型的数据结构，类似粉丝列表，文章的评论列表之类的东西。
  - 可以通过lrange命令，从某个元素开始读取多少个元素，可以基于list实现分页查询，基于Redis实现简单的高性能分页，可以做类似微博那种下拉不断分页的东西，性能高，就是一页一页走。
  - 可以制作一个简单的消息队列，从list头插入，从list 的尾巴取出

- Set

  - 无序列表，自动去重
  - 直接基于Set将系统中需要去重的数据丢进去，如果你需要对一些数据进行快速的全局去重，就可以使用基于JVM内存里的HashSet进行去重，但是如果你的某个系统部署在多台机器上的话，只有使用Redis进行全局的Set去重
  - 可以基于set玩儿交集、并集、差集的操作，比如交集吧，可以把两个人的粉丝列表整一个交集，看看俩人的共同好友是谁？把两个大v的粉丝都放在两个set中，对两个set做交集

- Sort Set

  - 排序的set，去重但是可以排序，写进去的时候给一个分数，自动根据分数排序，这个可以玩儿很多的花样，最大的特点是有个分数可以自定义排序规则
  - 比如说你要是想根据时间对数据排序，那么可以写入进去的时候用某个时间作为分数，人家自动给你按照时间排序了
  - 排行榜：将每个用户以及其对应的什么分数写入进去，zadd board score username，接着zrevrange board 0 99，就可以获取排名前100的用户；zrank board username，可以看到用户在排行榜里的排名

```
zadd board 85 zhangsan
zadd board 72 wangwu
zadd board 96 lisi
zadd board 62 zhaoliu

96 lisi
85 zhangsan
72 wangwu
62 zhaoliu

zrevrange board 0 3

获取排名前3的用户

96 lisi
85 zhangsan
72 wangwu

zrank board zhaoliu
```

###  Redis的线程模型

####  文件事件处理器

Redis基于reactor模式开发了网络事件处理器，这个处理器叫做文件事件处理器，file event handler，这个文件事件处理器是单线程的，因此Redis才叫做单线程的模型，采用IO多路复用机制同时监听多个socket，根据socket上的事件来选择相应的事件处理器来处理这个事件。

文件事件处理器是单线程模式下运行的，但是通过IO多路复用机制监听了多个socket，可以实现高性能的网络通信模型，又可以跟内部的其它单线程的模块进行对接，保证了Redis内部的线程模型的简单性。

文件事件处理器的结构包含4个部分：多个socket，IO多路复用程序，文件事件分派器，事件处理器等。

多个socket可能并发的产生不同的操作，每个操作对应不同的文件事件，但是IO多路复用程序会监听多个socket，但是会把socket放入到一个队列中排队，每次从队列中取出一个socket给事件分派器，事件分派器把socket给对应的时间处理器。

![image-20200421185741787](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/4_Redis%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200421185741787.png)

![image-20200421185725418](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/%E9%9D%A2%E8%AF%95%E6%89%AB%E7%9B%B2%E5%AD%A6%E4%B9%A0/4_Redis%E7%9A%84%E9%9D%A2%E8%AF%95%E8%BF%9E%E7%8E%AF%E7%82%AE/images/image-20200421185725418.png)

![image-20200623191724200](C:\Users\wangqian888935\AppData\Roaming\Typora\typora-user-images\redis线程模型.png)



![redis线程模型](D:\github\java-\java架构.assets\redis线程模型.png)



连接应答处理器：将socket01的AE_READABLE事件和命令请求处理器关联。

命令请求处理器：从socket01中读取出key和value，在自己的内存中完成key和value 的设置。将socket01的AE_WRITABLE事件关联起来

命令回复处理器：对socket01输出本次操作的结果，将socket01的AE_WRITABLE事件跟命令回复处理器解除关联。



每次我们一个socket请求过来 和 redis中的 server socket建立连接后，通过IO多路复用程序，就会往队列中插入一个socket，文件事件分派器就是将队列中的socket取出来，分派到对应的处理器，在处理器处理完成后，才会从队列中在取出一个。

这里也就是用一个线程，监听了客户端的所有请求，被称为Redis的单线程模型。



### 为什么Redis单线程模型效率这么高？

- 纯内存操作
- 核心是非阻塞的IO多路复用机制
- 单线程反而避免了多线程频繁上下文切换的问题



### Redis的过期策略

#### Redis数据为什么会丢失

之前有同学问过我，说我们生产环境的redis怎么经常会丢掉一些数据？写进去了，过一会儿可能就没了。我的天，同学，你问这个问题就说明redis你就没用对啊。redis是缓存，你给当存储了是吧？

啥叫缓存？用内存当缓存。内存是无限的吗，内存是很宝贵而且是有限的，磁盘是廉价而且是大量的。可能一台机器就几十个G的内存，但是可以有几个T的硬盘空间。redis主要是基于内存来进行高性能、高并发的读写操作的。

那既然内存是有限的，比如redis就只能用10个G，你要是往里面写了20个G的数据，会咋办？当然会干掉10个G的数据，然后就保留10个G的数据了。那干掉哪些数据？保留哪些数据？当然是干掉不常用的数据，保留常用的数据了。所以说，这是缓存的一个最基本的概念，数据是会过期的，要么是你自己设置个过期时间，要么是redis自己给干掉。

```
set key value 过期时间（1小时）
set进去的key，1小时之后就没了，就失效了
```

####  

#### 数据明明都过期了，怎么还占用着内存啊？

还有一种就是如果你设置好了一个过期时间，你知道redis是怎么给你弄成过期的吗？什么时候删除掉？如果你不知道，之前有个学员就问了，为啥好多数据明明应该过期了，结果发现redis内存占用还是很高？那是因为你不知道redis是怎么删除那些过期key的。

redis 内存一共是10g，你现在往里面写了5g的数据，结果这些数据明明你都设置了过期时间，要求这些数据1小时之后都会过期，结果1小时之后，你回来一看，redis机器，怎么内存占用还是50%呢？5g数据过期了，我从redis里查，是查不到了，结果过期的数据还占用着redis的内存。

#### 定期删除和惰性删除

我们Redis设置了过期时间，其实内部是 定期删除 + 惰性删除两个再起作用的。

所谓定期删除，指的是redis默认是每隔100ms就随机抽取一些设置了过期时间的key，检查其是否过期，如果过期就删除。假设redis里放了10万个key，都设置了过期时间，你每隔几百毫秒，就检查10万个key，那redis基本上就死了，cpu负载会很高的，消耗在你的检查过期key上了。注意，这里可不是每隔100ms就遍历所有的设置过期时间的key，那样就是一场性能上的灾难。实际上redis是每隔100ms随机抽取一些key来检查和删除的。

但是问题是，定期删除可能会导致很多过期key到了时间并没有被删除掉，那咋整呢？所以就是惰性删除了。这就是说，在你获取某个key的时候，redis会检查一下 ，这个key如果设置了过期时间那么是否过期了？如果过期了此时就会删除，不会给你返回任何东西。

并不是key到时间就被删除掉，而是你查询这个key的时候，redis再懒惰的检查一下

通过上述两种手段结合起来，保证过期的key一定会被干掉。

很简单，就是说，你的过期key，靠定期删除没有被删除掉，还停留在内存里，占用着你的内存呢，除非你的系统去查一下那个key，才会被redis给删除掉。

但是实际上这还是有问题的，如果定期删除漏掉了很多过期key，然后你也没及时去查，也就没走惰性删除，此时会怎么样？如果大量过期key堆积在内存里，导致redis内存块耗尽了，咋整？

答案是：走内存淘汰机制。



#### Redis内存淘汰机制

如果redis的内存占用过多的时候，此时会进行内存淘汰，有如下一些策略：

```
redis 10个key，现在已经满了，redis需要删除掉5个key

1个key，最近1分钟被查询了100次

1个key，最近10分钟被查询了50次

1个key，最近1个小时倍查询了1次
```

1）noeviction：当内存不足以容纳新写入数据时，新写入操作会报错，这个一般没人用吧，实在是太恶心了

2）allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key（这个是最常用的）

3）allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key，这个一般没人用吧，为啥要随机，肯定是把最近最少使用的key给干掉啊

4）volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key（这个一般不太合适）

5）volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key

6）volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除

很简单，你写的数据太多，内存满了，或者触发了什么条件，redis lru，自动给你清理掉了一些最近很少使用的数据



#### Redis中的LRU算法

Java版本的LRU

```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    
private final int CACHE_SIZE;

    // 这里就是传递进来最多能缓存多少数据
    public LRUCache(int cacheSize) {
        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true); // 这块就是设置一个hashmap的初始大小，同时最后一个true指的是让linkedhashmap按照访问顺序来进行排序，最近访问的放在头，最老访问的就在尾
        CACHE_SIZE = cacheSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > CACHE_SIZE; // 这个意思就是说当map中的数据量大于指定的缓存个数的时候，就自动删除最老的数据
    }
```

####  如何保证Redis的高并发及高可用？

如何保证Redis的高并发和高可用？

redis的主从复制原理能介绍一下么？

redis的哨兵原理能介绍一下么？

**剖析**

就是如果你用redis缓存技术的话，肯定要考虑如何用redis来加多台机器，保证redis是高并发的，还有就是如何让Redis保证自己不是挂掉以后就直接死掉了，redis高可用

我这里会选用我之前讲解过这一块内容，redis高并发、高可用、缓存一致性

redis高并发：主从架构，一主多从，一般来说，很多项目其实就足够了，单主用来写入数据，单机几万QPS，多从用来查询数据，多个从实例可以提供每秒10万的QPS。

redis高并发的同时，还需要容纳大量的数据：一主多从，每个实例都容纳了完整的数据，比如redis主就10G的内存量，其实你就最对只能容纳10g的数据量。如果你的缓存要容纳的数据量很大，达到了几十g，甚至几百g，或者是几t，那你就需要redis集群，而且用redis集群之后，可以提供可能每秒几十万的读写并发。

redis高可用：如果你做主从架构部署，其实就是加上哨兵就可以了，就可以实现，任何一个实例宕机，自动会进行主备切换。



## 高并发架构



## 高可用架构

