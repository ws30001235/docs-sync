## SDC接口调研

#### 一、SDC简介：

- SDC 是ONAP中设计和建模的工具。
- SDC 管理目录 ( catalog ) 中的内容并且可以按需组装 VNFs 。按照选择的工作流 ( workflow ) 和配置数据 ( configuration ) 组装特定的 VNFs 。
- SDC 定义了如何对 VNFs 进行部署、激活到生命周期管理。

##### SDC管理资源和服务：

以下的资源和服务又被称为 asset :

- 资源 ( Resource )：

  - 资源的定义：每个资源是一个或多个VFCs的组合，同时包含对其实例化、更新、删除、管理的全部信息。

  - 有三种类型的资源：
    - 基础设施  ( cloud 资源，如：计算、存储等)
    - 网络  (网络连接功能和元素，如：VNF )
    - 应用  (软件应用，如：一个实现负载均衡功能的应用)

- 服务 ( Service ) :

  - 服务的定义：每个服务是一个或多个资源的组合。

  - Service Designers 通过资源创建服务，同时包含对其实例化、更新、删除、管理的全部信息。

##### SDC的关键输出：

一组包含 asset 的功能描述及管理指令的模型。这些模型会被存储到 SDC Master Reference Catalog 供 ONAP 其他组件使用。

##### SDC的四个主要组件：

- 目录 (Catalog) : 存储资源和服务的仓库。
- Design Studio : 用于创建、修改、添加目录中资源和服务的定义。
- Certification Studio : 用于测试新的assets
- Distribution Studio : 用于部署经过测试验证过的assets

目前，SDC 从用于创建云基础设施的 **Heat Templates**、用于操控网络配置协议的状态数据的 **YANG XML 文件**、用于指定云基础设施的 **TOSCA 文件 **以及某些供应商提供的脚本中导入并保留信息**。

