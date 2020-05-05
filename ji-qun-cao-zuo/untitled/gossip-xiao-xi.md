# Gossip消息

Gossip协议的主要职责就是信息交换。信息交换的载体就是节点彼此发 送的Gossip消息，了解这些消息有助于我们理解集群如何完成信息交换。

常用的Gossip消息可分为：ping消息、pong消息、meet消息、fail消息等，它们的通信模式如图所示。

![](../../.gitbook/assets/image%20%28175%29.png)

* meet消息：用于通知新节点加入。消息发送者通知接收者加入到当前 集群，meet消息通信正常完成后，接收节点会加入到集群中并进行周期性的 ping、pong消息交换。
* ping消息：集群内交换最频繁的消息，集群内每个节点每秒向多个其 他节点发送ping消息，用于检测节点是否在线和交换彼此状态信息。ping消 息发送封装了自身节点和部分其他节点的状态数据。
* pong消息：当接收到ping、meet消息时，作为响应消息回复给发送方确 认消息正常通信。pong消息内部封装了自身状态数据。节点也可以向集群内 广播自身的pong消息来通知整个集群对自身状态进行更新。
* fail消息：当节点判定集群内另一个节点下线时，会向集群内广播一个 fail消息，其他节点接收到fail消息之后把对应节点更新为下线状态。具体细节将在后面“故障转移”中说明。

所有的消息格式划分为：消息头和消息体。消息头包含发送节点自身状态数据，接收节点根据消息头就可以获取到发送节点的相关数据，结构如下：

![](../../.gitbook/assets/image%20%28212%29.png)

集群内所有的消息都采用相同的消息头结构clusterMsg，它包含了发送 节点关键信息，如节点id、槽映射、节点标识（主从角色，是否下线）等。 消息体在Redis内部采用clusterMsgData结构声明，结构如下：

```text
union clusterMsgData {
    /* ping,meet,pong消息体*/ 
    struct {
        /* gossip消息结构数组 */
        clusterMsgDataGossip gossip[1];
    } ping; 
    /* FAIL 消息体 */ 
    struct { 
         clusterMsgDataFail about; 
     } fail; 
// ...
};
```

消息体clusterMsgData定义发送消息的数据，其中ping、meet、pong都采 用cluster MsgDataGossip数组作为消息体数据，实际消息类型使用消息头的 type属性区分。每个消息体包含该节点的多个clusterMsgDataGossip结构数 据，用于信息交换，结构如下：

![](../../.gitbook/assets/image%20%28158%29.png)

当接收到ping、meet消息时，接收节点会解析消息内容并根据自身的识 别情况做出相应处理，对应流程如图所示。

![](../../.gitbook/assets/image%20%2817%29.png)

接收节点收到ping/meet消息时，执行解析消息头和消息体流程：

* 解析消息头过程：消息头包含了发送节点的信息，如果发送节点是新 节点且消息是meet类型，则加入到本地节点列表；如果是已知节点，则尝试 更新发送节点的状态，如槽映射关系、主从角色等状态。
* 解析消息体过程：如果消息体的clusterMsgDataGossip数组包含的节点

  是新节点，则尝试发起与新节点的meet握手流程；如果是已知节点，则根据 cluster MsgDataGossip中的flags字段判断该节点是否下线，用于故障转移。

消息处理完后回复pong消息，内容同样包含消息头和消息体，发送节点 接收到回复的pong消息后，采用类似的流程解析处理消息并更新与接收节点 最后通信时间，完成一次消息通信。

