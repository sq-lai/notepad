# 一、Nacos 和 Gateway

## Nacos 注册中心

### 1. 什么是注册中心？

**注册中心**在微服务架构中的作用是**负责服务的注册与发现**。每个服务启动后会把自己的信息**（如地址、端口）**注册到注册中心，**其他服务可以通过注册中心查找到这个服务的信息，实现服务间的通信。**

### 2. Nacos 注册中心的作用

Nacos 是 Alibaba 开源的一个动态服务发现、配置管理和服务管理平台。具体作用包括：

- **服务注册**：微服务启动时将自己的信息注册到 Nacos。
- **服务发现**：其他微服务可以通过服务名从 Nacos 查询到对应的实例信息。
- **健康检查**：Nacos 定期检查服务是否可用，并标记不可用的服务实例为下线。

### 3. 使用 Nacos 注册中心的步骤

- **服务注册**
- **服务发现**
- **健康检查**

## Nacos 配置中心

### 1. 什么是配置中心？

配置中心是用来**集中管理微服务应用配置的地方**，比如数据库连接信息、接口地址等。**它允许实时更新配置，而无需重启服务。**

### 2. Nacos 配置中心的作用

Nacos 作为配置中心，允许我们统一托管配置文件，**并在多个环境下共享、管理和动态更新配置。**

### 3. 使用 Nacos 配置中心的步骤

- **配置存储**：在 Nacos 控制台中添加或修改配置项。
- **客户端获取配置**：微服务通过 Nacos 客户端 API 获取所需配置信息。
- **动态刷新**：服务可以监听配置变化，自动更新到最新配置。

**实例讲解**

```properties
spring.application.name=service-customer     //应用的名字，在微服务架构中，这个名字通常用于服务注册和发现。
spring.profiles.active=dev    //激活 dev 配置文件，可以根据环境（开发、测试、生产）来加载不同的配置。
spring.main.allow-bean-definition-overriding=true    //允许在 Spring 容器中覆盖已存在的 Bean 定义，解决 Bean 冲突。
spring.cloud.nacos.discovery.server-addr=192.168.6.129:8848   //指定 Nacos 注册中心的地址，微服务会在这个地址进行服务注册和发现。
spring.cloud.nacos.config.server-addr=192.168.6.129:8848    //指定 Nacos 配置中心的地址，用于管理和获取配置信息。
spring.cloud.nacos.config.prefix=${spring.application.name}  //设置配置的前缀，通常使用应用名称。
spring.cloud.nacos.config.file-extension=yaml    //配置文件的格式，指定为 yaml。
spring.cloud.nacos.config.shared-configs[0].data-id=common-account.yaml   //指定共享配置的 data-id，可以理解为配置文件的名字，用于加载公共配置。
```



## Gateway 网关

### 1. 什么是网关？

网关是进入**微服务架构系统的唯一入口**，负责请求的路由、负载均衡、安全验证，以及其他请求操作。

### 2. Gateway 网关的作用

Spring Cloud Gateway 是基于 Spring WebFlux 构建的响应式、异步网关，用于高效路由请求、转换协议和增强服务功能。

### 3. 使用 Gateway 网关的步骤

- **定义路由**：通过**配置文件或 Java 代码**定义路由，决定**请求如何被转发到具体的微服务**。
- **过滤器链**：定义一系列**过滤器处理请求**，如**身份验证、日志记录、修改请求头**等。
- **负载均衡**：Gateway 将请求按规则**分发到多个实例，优化负载。**