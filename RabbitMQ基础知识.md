### 模型

![avatar](http://wx1.sinaimg.cn/mw690/007h1WTYly1fv4qbuc5vxj30ku0bh0yw.jpg)

### 基本组成
#### Queue
Queue(队列)RabbitMQ的作用是存储消息，队列的特性是先进先出。
![avatar](http://wx2.sinaimg.cn/mw690/007h1WTYly1fuyxd372soj308002s3yc.jpg)

#### Exchange
Exchange将消息路由到一个或者多个Queue中（或者丢弃）

* fanout：路由规则非常简单，它会把所有发送到该Exchange的消息路由到所有与它绑定的Queue中。

* direct：路由规则也很简单，它会把消息路由到那些binding key与routing key完全匹配的Queue中。

![avatar](http://wx3.sinaimg.cn/mw690/007h1WTYly1fv4og2weeaj30bc04rglm.jpg)

* topic：将消息路由到binding key与routing key相匹配的Queue中，但这里的匹配规则有些不同，它约定：
	* routing key为一个句点号“. ”分隔的字符串（我们将被句点号“. ”分隔开的每一段独立的字符串称为一个单词），如“stock.usd.nyse”、“nyse.vmw”、“quick.orange.rabbit”
	* binding key与routing key一样也是句点号“. ”分隔的字符串
	* binding key中可以存在两种特殊字符“\*”与“#”，用于做模糊匹配，其中“*”用于匹配一个单词，“#”用于匹配多个单词（可以是零个）
![avatar](http://wx2.sinaimg.cn/mw690/007h1WTYly1fv4oj30vx5j30bs04rq2y.jpg)


* headers：不依赖于routing key与binding key的匹配规则来路由消息，而是根据发送的消息内容中的headers属性进行匹配。在绑定Queue与Exchange时指定一组键值对；当消息发送到Exchange时，RabbitMQ会取到该消息的headers（也是一个键值对的形式），对比其中的键值对是否完全匹配Queue与Exchange绑定时指定的键值对；如果完全匹配则消息会路由到该Queue，否则不会路由到该Queue。(基本不用)

#### VirtualHost
每个VirtualHost相当月一个相对独立的RabbitMQ服务器，每个VirtualHost之间是相互隔离的。exchange、queue、message不能互通。 

### ack机制


### 高可用
#### 镜像模式
镜像模式和普通模式的区别就是，队列的数据都镜像了一份到所有的节点上。这样任何一个节点失效，不会影响整个集群的使用。 集群通过选举算法，选出一个master和若干个slaver。

* consumer，任意连接一个节点，若连上的不是master，请求会转发给master，为了保证消息的可靠性，consumer回复ack给master后，master删除消息并广播所有的slaver去删除。 
* publisher ，任意连接一个节点，若连上的不是master，则转发给master，由master存储并转发给其他的slaver存储。 


