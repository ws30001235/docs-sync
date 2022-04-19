## TOSCA模型

TOSCA模型使用YAML格式编写。TOSCA的部署是基于虚拟机的。

TOSCA 被设计为一种用于部署和管理云服务的自动化语言。与许多其他自动化工具不同，TOSCA 是**声明式**的。这种方法首先*描述*需要部署的内容，而不是*规定*部署需要采取的步骤。在 TOSCA 中，此描述由一个*服务模型*组成，该模型包含构成服务的组件（TOSCA 节点）以及这些组件之间的关系。

在ONAP中，TOSCA模型被SDC用来描述服务、资源及其关系。可以通过API、用户界面或运行自定义脚本来定义新的资源类型。在上传TOSCA模型后，SDC将这些新的资源类型存储在目录中，并提供给设计者使用。

通过VNF Package Tool，制作一个VNF包，需要VDU镜像、用于安装配置镜像的脚本文件以及为各个节点配置TOSCA模板文件。

##### TOSCA对象模型

TOSCA 模型是图
TOSCA 使用*图*作为服务的核心建模结构：

- TOSCA 服务被建模为由一组节点以及这些节点之间的关系组成的拓扑图。节点是服务图中的顶点，关系是图中的边。

- TOSCA 节点有意尽可能通用。节点本质上只不过是命名属性及其相关值的集合。节点被命名，并使用节点类型进行标记。

- 关系是 TOSCA 图中的第一类实体。就像节点一样，关系也有属性、标签和名称。

  

TOSCA 提供了节点类型的类型系统来描述构建服务模板的可能构建块，以及关系类型来描述可能的关系类型。节点和关系类型都可以定义生命周期操作来实现编排引擎在实例化服务模板时可以调用的行为。例如，某些软件产品的节点类型可能提供“创建”操作来处理运行时组件实例的创建，或“开始”或“停止”操作来处理由编排引擎触发的开始或停止事件。这些生命周期操作由实现实际行为的脚本或 Chef 配方等实现工件提供支持。



TOSCA 模型将这三个区域连接在一起，如下图所示：

[![img](https://3.bp.blogspot.com/-rSacFY7Za7A/Ws0Q0Evd8rI/AAAAAAAAAOA/RKllN3A7DoQEgyp8rmO19ogOGcQ7HGn2wCLcBGAs/s640/meta.png)](https://3.bp.blogspot.com/-rSacFY7Za7A/Ws0Q0Evd8rI/AAAAAAAAAOA/RKllN3A7DoQEgyp8rmO19ogOGcQ7HGn2wCLcBGAs/s1600/meta.png)

- 运行时实例（即实例模型中的节点）保留对创建它们的节点模板的引用
- 节点模板保留对节点类型的引用，该节点类型为节点中的属性定义模式。
- 节点类型提供了将通用节点转换为可重用的特定领域组件的规范。

##### [TOSCA简单配置文件](http://blog.ubicity.com/2016/07/tosca-simple-profile-in-yaml.html)

TOSCA Simple Profile 规范重要的贡献是引入了基本*类型系统*。它为大多数云服务和基础架构组件提供高级抽象。可以扩展和组合成更复杂的抽象，以适合特定领域的服务。

规范类型不仅针对*云应用程序*软件（包括 Web、应用程序和数据库层）定义，还针对*基础设施*组件（例如计算、存储和网络）定义。这意味着 TOSCA 可以用作*统一的建模语言*适用于云堆栈中的所有层，包括 SDN 和 NFV。

TOSCA 简单配置文件还引入了*标准化的生命周期管理*TOSCA 中定义的实体的接口。

##### TOSCA替换映射

TOSCA 通过替换映射的概念支持不同抽象级别之间的转换。使用替换映射，服务拓扑中的任何抽象节点都可以替换为整个子拓扑（在不同的服务模板中描述），该子拓扑提供较低抽象级别所需的附加细节（功能和结构）。替换映射过程将较高级别节点的能力、要求和属性映射到代表较低抽象级别的子拓扑中节点的等效能力、要求和属性。

可以递归地应用替换映射，直到消除所有抽象节点并且服务拓扑完全由可以部署到可用基础设施资源上的具体节点组成。



##### 描述VNF的TOSCA模型的节点定义

| VNFD types {number}   | derived_from                     | 描述                                   |
| --------------------- | -------------------------------- | -------------------------------------- |
| data_types{52}        | tosca.datatypes.Root             | 节点使用的数据类型                     |
| artifact_types{2}     | tosca.artifacts.Deployment.Image | 节点使用的工件类型                     |
| capability_types{4}   | tosca.capabilities.Root          | 描述节点具备的功能                     |
| relationship_types{3} | tosca.relationships.DependsOn    | 定义节点之间的关系                     |
| interface_types{1}    | tosca.interfaces.Root            | 节点具备的接口类型                     |
| node_types{9}         | tosca.nodes.Root                 | 定义一个或多个节点模板类型的可重用实体 |
| group_types{1}        | tosca.groups.Root                | 定义节点的逻辑分组类型                 |
| policy_types{12}      | tosca.policies.Root              | 定义节点的策略类型                     |



以数据类型举例

| 名称                              | 描述                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| tosca.datatypes.nfv.L2AddressData | Describes the information on the MAC addresses to be assigned to a connection point. |
| tosca.datatypes.nfv.L3AddressData | Provides information about Layer 3 level addressing scheme and parameters applicable to a CP |
| tosca.datatypes.nfv.AddressData   | Describes information about the addressing scheme and parameters applicable to a CP |
| ...                               | ...                                                          |