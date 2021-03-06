CLI工具配置及使用方法
====================

使用命令行界面工具（CLI）可以实现方便快捷的集群管理。利用此工具，可以查看集群及各节点的状态，并进行各节点、卷及用户的管理。

随着CLI的不断完善，最终将会实现对于集群各节点接口功能的100%覆盖。

编译及配置
----------

下载ChubaoFS源码后，在 ``chubaofs/cli`` 目录下，运行 ``build.sh`` 文件 ，即可生成 ``cfs-cli`` 可执行程序。

同时，在 ``root`` 目录下会生成名为 ``.cfs-cli.json`` 的配置文件，修改master地址为当前集群的master地址即可。也可使用命令 ``./cfs-cli config info`` 和 ``./cfs-cli config set`` 来查看和设置配置文件。

使用方法
---------

在 ``chubaofs/cli`` 目录下，执行命令 ``./cfs-cli --help`` 或 ``./cfs-cli -h`` ，可获取CLI的帮助文档。

CLI主要分为六类管理命令：

.. csv-table::
   :header: "命令", "描述"

   "cfs-cli cluster", "集群管理"
   "cfs-cli metanode", "元数据节点管理"
   "cfs-cli datanode", "数据节点管理"
   "cfs-cli datapartition", "数据分片管理"
   "cfs-cli metapartition", "元数据分片管理"
   "cfs-cli config", "配置管理"
   "cfs-cli completion", "生成自动补全命令脚本"
   "cfs-cli volume, vol", "卷管理"
   "cfs-cli user", "用户管理"
   "cfs-cli compatibility", "兼容性测试"

集群管理命令
>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli cluster info          #获取集群信息，包括集群名称、地址、卷数量、节点数量及使用率等

.. code-block:: bash

    ./cfs-cli cluster stat          #按区域获取元数据和数据节点的使用量、状态等

.. code-block:: bash

    ./cfs-cli cluster freeze [true/false]        #是否冻结集群，设置为 `true` 冻结后，当partition写满，集群不会自动分配新的partition

.. code-block:: bash

    ./cfs-cli cluster threshold [float]     #设置集群中每个MetaNode的内存阈值


元数据节点管理命令
>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli metanode list         #获取所有元数据节点的信息，包括id、地址、读写状态及存活状态

.. code-block:: bash

    ./cfs-cli metanode info [Address]     #展示元数据节点基本信息，包括状态、使用量、承载的partition ID等，

.. code-block:: bash

    ./cfs-cli metanode decommission [Address] #将该元数据节点下线，该节点上的partition将自动转移至其他可用节点


数据节点管理命令
>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli datanode list         #获取所有数据节点的信息，包括id、地址、读写状态及存活状态

.. code-block:: bash

    ./cfs-cli datanode info [Address]     #展示数据节点基本信息，包括状态、使用量、承载的partition ID等，

.. code-block:: bash

    ./cfs-cli datanode decommission [Address] #将该数据节点下线，该节点上的data partition将自动转移至其他可用节点


数据分片管理命令
>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli datapartition info [VOLUME] [Partition ID]        #获取指定data partition的信息

.. code-block:: bash

    ./cli datapartition decommission [Address] [Partition ID]   #将目标节点上的指定data partition分片下线，并自动转移至其他可用节点

.. code-block:: bash

    ./cfs-cli datapartition add-replica [Address] [Partition ID]    #在目标节点新增一个data partition分片

.. code-block:: bash

    ./cfs-cli datapartition del-replica [Address] [Partition ID]    #删除目标节点上的data partition分片

.. code-block:: bash

    ./cfs-cli datapartition check    #故障诊断，查找多半分片不可用和分片缺失的data partition


元数据分片管理命令
>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli metapartition info [VOLUME] [Partition ID]        #获取指定meta partition的信息

.. code-block:: bash

    ./cli metapartition decommission [Address] [Partition ID]   #将目标节点上的指定meta partition分片下线，并自动转移至其他可用节点

.. code-block:: bash

    ./cfs-cli metapartition add-replica [Address] [Partition ID]    #在目标节点新增一个meta partition分片

.. code-block:: bash

    ./cfs-cli metapartition del-replica [Address] [Partition ID]    #删除目标节点上的meta partition分片

.. code-block:: bash

    ./cfs-cli metapartition check    #故障诊断，查找多半分片不可用和分片缺失的meta partition

配置管理
>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli config info     #展示配置信息

.. code-block:: bash

    ./cfs-cli config set     #设置配置信息
    root@fa27e115a0ba:/cfs#$ cfs-cli config set
    Please input master host:
    test.chubaofs.com
    Config has been set successfully!


自动补全管理
>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli completion      #生成命令自动补全脚本

卷管理命令
>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli volume create [VOLUME NAME] [USER ID] [flags]     #创建所有者是[USER ID]的卷[VOLUME NAME]
    Flags:
        --capacity uint                                     #指定卷的容量，单位GB（默认为10）
        --dp-size  uint                                     #指定数据分片的大小，单位GB（默认为120）
        --follower-read                                     #启用从follower副本中读取数据的功能（默认为true）
        --mp-count int                                      #指定初始元数据分片的数量（默认为3）
        -y, --yes                                           #跳过所有问题并设置回答为"yes"

.. code-block:: bash

    ./cfs-cli volume delete [VOLUME NAME] [flags]               #删除指定卷[VOLUME NAME]
    Flags:
        -y, --yes                                           #跳过所有问题并设置回答为"yes"

.. code-block:: bash

    ./cfs-cli volume info [VOLUME NAME] [flags]                 #获取卷[VOLUME NAME]的信息
    Flags:
        -d, --data-partition                                #显示数据分片的详细信息
        -m, --meta-partition                                #显示元数据分片的详细信息

.. code-block:: bash

    ./cfs-cli volume add-dp [VOLUME] [NUMBER]                   #创建并添加个数为[NUMBER]的数据分片至卷[VOLUME]

.. code-block:: bash

    ./cfs-cli volume list                                       #获取包含当前所有卷信息的列表

.. code-block:: bash

    ./cfs-cli volume transfer [VOLUME NAME] [USER ID] [flags]   #将卷[VOLUME NAME]转交给其他用户[USER ID]
    Flags：
        -f, --force                                         #强制转交
        -y, --yes                                           #跳过所有问题并设置回答为"yes"

用户管理命令
>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli user create [USER ID] [flags]         #创建用户[USER ID]
    Flags：
        --access-key string                     #指定用户用于对象存储功能的access key
        --secret-key string                     #指定用户用于对象存储功能的secret key
        --password string                       #指定用户密码
        --user-type string                      #指定用户类型，可选项为normal或admin（默认为normal）
        -y, --yes                               #跳过所有问题并设置回答为"yes"

.. code-block:: bash

    ./cfs-cli user delete [USER ID] [flags]         #删除用户[USER ID]
    Flags：
        -y, --yes                               #跳过所有问题并设置回答为"yes"

.. code-block:: bash

    ./cfs-cli user info [USER ID]                   #获取用户[USER ID]的信息

.. code-block:: bash

    ./cfs-cli user list                             #获取包含当前所有用户信息的列表

.. code-block:: bash

    ./cfs-cli user perm [USER ID] [VOLUME] [PERM]   #更新用户[USER ID]对于卷[VOLUME]的权限[PERM]
                                                #[PERM]可选项为"只读"（READONLY/RO）、"读写"（READWRITE/RW）、"删除授权"（NONE）

.. code-block:: bash

    ./cfs-cli user update [USER ID] [flags]         #更新用户[USER ID]的信息
    Flags：
        --access-key string                     #更新后的access key取值
        --secret-key string                     #更新后的secret key取值
        --user-type string                      #更新后的用户类型，可选项为normal或admin
        -y, --yes                               #跳过所有问题并设置回答为"yes"


兼容性测试
>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

    ./cfs-cli cptest meta [Snapshot Path] [Host] [Partition ID]         #meta data 兼容性测试
    Parameters：
            [Snapshot Path] string                     #快照文件存放路径
            [Host] string                              #生成快照文件的MetaNode地址
            [Partition ID] string                      #需要测试对比的meta partition ID
例:
    1. 使用旧版本server生成meta data, 停止meta data服务, 五分钟后会生成meta data快照数据, 然后拷贝快照文件到本地目录
    2. 在本地机器执行 `cfs-cli cptest meta` 命令对刚刚拷贝的旧版本快照数据和线上的新数据对比验证

    .. code-block:: bash

        [Verify result]
        All dentry are consistent
        All inodes are consistent
        All meta has checked
