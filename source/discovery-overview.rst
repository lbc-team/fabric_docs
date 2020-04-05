服务发现
=================

Why do we need service discovery?
---------------------------------

为什么我们需要服务发现？
---------------------------------

In order to execute chaincode on peers, submit transactions to orderers, and to
be updated about the status of transactions, applications connect to an API
exposed by an SDK.

应用程序通过连接 SDK 暴露的 API 完成这些事情 —— 在 peers 上执行链码，将交易提交给 orderers，更新交易的状态。

However, the SDK needs a lot of information in order to allow applications to
connect to the relevant network nodes. In addition to the CA and TLS certificates
of the orderers and peers on the channel -- as well as their IP addresses and port
numbers -- it must know the relevant endorsement policies as well as which peers
have the chaincode installed (so the application knows which peers to send chaincode
proposals to).

但是，SDK 需要大量信息，以便允许应用程序连接到相关的网络节点。除了通道中 orderers 与 peers 的 CA 与 TLS 证书
，与它们的 IP 地址与端口；还要提供相关的背书策略，和告知链码安装在哪些 peers 上（便于让应用程序知道要将链码提案发
给哪些 peers ）。

Prior to v1.2, this information was statically encoded. However, this implementation
is not dynamically reactive to network changes (such as the addition of peers who have
installed the relevant chaincode, or peers that are temporarily offline). Static
configurations also do not allow applications to react to changes of the
endorsement policy itself (as might happen when a new organization joins a channel).

在 v1.2 之前，这些信息是静态编码的。然而，这种实现并不能对网络的改变进行动态响应（例如添加已安装相关链码的 peers，
或者 peers 临时掉线）。静态配置也不允许应用程序响应背书策略本身的更改（如新组织加入通道时可能发生的情况）。

In addition, the client application has no way of knowing which peers have updated
ledgers and which do not. As a result, the application might submit proposals to
peers whose ledger data is not in sync with the rest of the network, resulting
in transaction being invalidated upon commit and wasting resources as a consequence.

此外，客户端应用程序无法知道哪些 peers 已更新账本，哪些未更新。 结果，该应用程序可能向「其账本数据与
网络其余部分不同步」的 peers 提交提案，从而导致交易在提交时被拒，最终导致浪费了资源。

The **discovery service** improves this process by having the peers compute
the needed information dynamically and present it to the SDK in a consumable
manner.

**服务发现** 通过「让 peers 来计算所需动态信息，然后提供给 SDK 去使用」这种方式改善了这个过程。

How service discovery works in Fabric
-------------------------------------

服务发现在 Fabric 中是怎样工作的？
-------------------------------------

The application is bootstrapped knowing about a group of peers which are
trusted by the application developer/administrator to provide authentic responses
to discovery queries. A good candidate peer to be used by the client application
is one that is in the same organization. Note that in order for peers to be known
to the discovery service, they must have an ``EXTERNAL_ENDPOINT`` defined. To see
how to do this, check out our :doc:`discovery-cli` documentation.

应用程序在启动时知道可访问哪些对于应用开发人员/管理员而言可信的 peers，以提供对「发现查询」的可信响应。
和应用程序在同一个组织内的 peer 会是一个好的候选 peer。请注意，为了使服务发现知晓 peers，必须为 peers
定义 ``EXTERNAL_ENDPOINT``。 要查看如何执行此操作，请查看我们的 :doc:`discovery-cli` 文档。

The application issues a configuration query to the discovery service and obtains
all the static information it would have otherwise needed to communicate with the
rest of the nodes of the network. This information can be refreshed at any point
by sending a subsequent query to the discovery service of a peer.

应用程序向服务发现发送一个配置查询，即可获得网络的静态信息，以供与网络中的其它节点进行通讯。
这些信息随时可以刷新 —— 通过向一个 peer 的服务发现发送后续查询的请求。

The service runs on peers -- not on the application -- and uses the network metadata
information maintained by the gossip communication layer to find out which peers
are online. It also fetches information, such as any relevant endorsement policies,
from the peer's state database.

服务发现运行于 peers 上，利用 gossip 通讯层维护的元数据来得知哪些 peer 在线。服务发现也从 peer 节
点的状态数据库提取「背书策略」等信息。

With service discovery, applications no longer need to specify which peers they
need endorsements from. The SDK can simply send a query to the discovery service
asking which peers are needed given a channel and a chaincode ID. The discovery
service will then compute a descriptor comprised of two objects:


有了服务发现，应用程序不必再指定要从哪些 peer 获得背书。SDK 只要依据给定的通道和链码 ID，向发现
服务发送一个简单的查询。服务发现就可以计算出包含下面两个对象的描述：

1. **Layouts**: a list of groups of peers and a corresponding amount of peers from
   each group which should be selected.
2. **Group to peer mapping**: from the groups in the layouts to the peers of the
   channel. In practice, each group would most likely be peers that represent
   individual organizations, but because the service API is generic and ignorant of
   organizations this is just a "group".

1. **布局**：peers 的分组清单，和每组中选出的相应数量的 peers。
2. **分组和 peer 的映射**：从布局中的分组到通道中的 peers。实践中，每一个分组通常由个体组织
中的 peers 构成（例如，某公司的 peers 是一个分组），但是因为服务 API 是通用的，因而忽略组织，使用「
分组」这个概念。

The following is an example of a descriptor from the evaluation of a policy of
``AND(Org1, Org2)`` where there are two peers in each of the organizations.

下面的示例描述了两个组织，每个组织中包含两个peer节点，且采用 ``AND(Org1, Org2)`` 的评估策略：

.. code-block:: JSON

   Layouts: [
        QuantitiesByGroup: {
          “Org1”: 1,
          “Org2”: 1,
        }
   ],
   EndorsersByGroups: {
     “Org1”: [peer0.org1, peer1.org1],
     “Org2”: [peer0.org2, peer1.org2]
   }

In other words, the endorsement policy requires a signature from one peer in Org1
and one peer in Org2. And it provides the names of available peers in those orgs who
can endorse (``peer0`` and ``peer1`` in both Org1 and in Org2).

换而言之，背书策略要求 Org1 中的一个 peer 和 Org2 中的一个 peer 共同签名参与背书。此外，描述还提供了
组织中可用来背书的可用节点的名称（Org1 和 Org2 中的 ``peer0`` 和 ``peer1`` ）。

The SDK then selects a random layout from the list. In the example above, the
endorsement policy is Org1 ``AND`` Org2. If instead it was an ``OR`` policy, the SDK
would randomly select either Org1 or Org2, since a signature from a peer from either
Org would satisfy the policy.

SDK 则从上述描述中随机选择一个布局。上例中，背书策略是 Org1 ``AND`` Org2 。如果 背书策略是 ``OR``
的话，SDK 会随机的选择 Org1 或者 Org2。因此两个组织中的任一个 peer 的签名既可满足背书策略。

After the SDK has selected a layout, it selects from the peers in the layout based on a
criteria specified on the client side (the SDK can do this because it has access to
metadata like ledger height). For example, it can prefer peers with higher ledger heights
over others -- or to exclude peers that the application has discovered to be offline
-- according to the number of peers from each group in the layout. If no single
peer is preferable based on the criteria, the SDK will randomly select from the peers
that best meet the criteria.

SDK 选择布局后，它会依据客户端指定的标准选择 peers（因为知道账本高度，所以 SDK 能做这件事）。例如，依据
布局中每个节点的组的数量， SDK 可以选择账本高度高的 peer，或者排除已下线的 peer。如果没有 peer 满足标准，
SDK 则随机选择次优 peers。

Capabilities of the discovery service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

服务发现的功能
~~~~~~~~~~~~

The discovery service can respond to the following queries:

服务发现可以对如下的请求响应：

* **Configuration query**: Returns the ``MSPConfig`` of all organizations in the channel
  along with the orderer endpoints of the channel.
* **Peer membership query**: Returns the peers that have joined the channel.
* **Endorsement query**: Returns an endorsement descriptor for given chaincode(s) in
  a channel.
* **Local peer membership query**: Returns the local membership information of the
  peer that responds to the query. By default the client needs to be an administrator
  for the peer to respond to this query.

* **配置查询**：返回通道中所有组织的「MSPConfig」以及通道的 orderer 端。
* **Peers 成员查询**：返回已加入通道的 peers。
* **背书查询**：返回channel 中给定链码的背书策略描述。
* **本地 peers 成员查询**：返回查询请求中的 peer 节点的本地成员信息。缺省情况下， peer 节点需要
客户端具有管理员权限，才能回应这一请求。

Special requirements
~~~~~~~~~~~~~~~~~~~~~~

特殊需求
~~~~~~~

When the peer is running with TLS enabled the client must provide a TLS certificate when connecting
to the peer. If the peer isn't configured to verify client certificates (clientAuthRequired is false), this TLS certificate
can be self-signed.


当 peer 节点使用 TLS 时，客户端必须提供 TLS 证书才能连接 peer。如果 peer 节点没有配置要验证客户端的证书
（clientAuthRequired 为 false），TLS 证书可以自我验签。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
