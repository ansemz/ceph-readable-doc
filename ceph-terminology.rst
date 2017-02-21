===========
 Ceph 术语
===========

以下是我对术语的理解和翻译，摘录自各种文档或邮件列表，若有不当之处，敬请赐教！


Cache Tiering
-------------


cluster map 集群运行图
----------------------
集群处于动态的运行中，配置会变更、OSD会up/down，所以把它理解为静态的图是不对的；尤其对大型集群来说，当机、硬件故障是常态。但是在理解、分析时，提取的片段都可以当作静态的，就像拍下的照片。


epoch 时间结
------------
epoch原意是“新纪元，时代，时期，时间上的一点”，我想作者的意思大概就是每隔一段时间总结一下，汇报下某段时间的事件。大概类似于朝代更迭，只是时间短点而以。

last epoch start
''''''''''''''''
the last epoch at which all nodes in the acting set for a particular placement group agreed on an authoritative history. At this point, peering is deemed to have been successful.

last epoch clean
''''''''''''''''
the last epoch at which all nodes in the acting set for a particular placement group were completely up to date (both PG logs and object contents). At this point, recovery is deemed to have been completed.


erasure coding 纠删码
---------------------
Erasure coding (EC) is a method of data protection in which data is broken into fragments, expanded and encoded with redundant data pieces and stored across a set of different locations, such as disks, storage nodes or geographic locations.
The goal of erasure coding is to enable data that becomes corrupted at some point in the disk storage process to be reconstructed by using information about the data that's stored elsewhere in the array.
Erasure coding creates a mathematical function to describe a set of numbers so they can be checked for accuracy and recovered if one is lost. Referred to as polynomial interpolation or oversampling, this is the key concept behind erasure codes. In mathematical terms, the protection offered by erasure coding can be represented in simple form by the following equation: n = k + m. The variable “k” is the original amount of data or symbols. The variable “m” stands for the extra or redundant symbols that are added to provide protection from failures. The variable “n” is the total number of symbols created after the erasure coding process.
For instance, in a 10 of 16 configuration, or EC 10/16, six extra symbols (m) would be added to the 10 base symbols (k). The 16 data fragments (n) would be spread across 16 drives, nodes or geographic locations. The original file could be reconstructed from 10 verified fragments.
Erasure codes, also known as forward error correction (FEC) codes, were developed more than 50 years ago. Different types have emerged since that time. In one of the earliest and most common types, Reed-Solomon, the data can be reconstructed using any combination of “k” symbols, or pieces of data, even if “m” symbols are lost or unavailable. For example, in EC 10/16, six drives, nodes or geographic locations could be lost or unavailable, and the original file would still be recoverable.
Erasure coding can be useful with large quantities of data and any applications or systems that need to tolerate failures, such as disk array systems, data grids, distributed storage applications, object stores and archival storage. One common current use case for erasure coding is object-based cloud storage


intent log 意图日志
-------------------
src/rgw/rgw_rados.h:
to notify upper layer that we need to do some operation on an object, and it's up to
the upper layer to schedule this operation.. e.g., log intent in intent log


keystone
--------
Keystone 是 OpenStack 项目的子项目，提供身份识别、令牌、目录和策略服务。实现了 OpenStack 的身份识别 API 。


peering
-------


pg（placement group）归置组
---------------------------
placement意思是放置、配置的意思，是静态的；而归置含有整理、放好的意思，是动态过程。但纵观全文，每次用CRUSH算法计算出的结果都是静态的，经常变的只是CRUSH计算时的输入，所以从整体来说是“归置”，而从局部来说都是“放置”。

pg log
''''''
a list of recent updates made to objects in a PG. Note that these logs can be truncated after all OSDs in the acting set have acknowledged up to a certain point.

primary
'''''''
the (by convention first) member of the acting set, who is responsible for coordination peering, and is the only OSD that will accept client initiated writes to objects in a placement group.

recovery
''''''''
ensuring that copies of all of the objects in a PG are on all of the OSDs in the acting set. Once peering has been performed, the primary can start accepting write operations, and recovery can proceed in the background.


region
------
region 是地理空间的逻辑划分，它包含一个或多个 zone 。一个包含多个 region 的集群必须指定一个主 region 。


replica
-------
a non-primary OSD in the acting set for a placement group (and who has been recognized as such and activated by the primary).

stray
-----
an OSD who is not a member of the current acting set, but has not yet been told that it can delete its copies of a particular placement group.


\* set
------

acting set
''''''''''
一个归置组的数据同时分布于多个OSD，也就是说这些OSD负责这个归置组，这些OSD就称为acting set。也是个变化的集合。

hit set
'''''''

missing set
'''''''''''
Each OSD notes update log entries and if they imply updates to the contents of an object, adds that object to a list of needed updates. This list is called the missing set for that <OSD,PG>.

up set
''''''
是acting set中处于up状态的那部分OSD。


zone
----
zone 是一个或多个 Ceph 对象网关例程的逻辑分组。每个 region 有一个主 zone 处理客户端请求。
