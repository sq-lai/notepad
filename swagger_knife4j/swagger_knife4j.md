# 基本概念理解区分

### 设计 RESTful API

- 在设计 RESTful API 时，你需要遵循 **RESTful API 规范**，确保每个接口是标准化的。
- 你可以使用 **Swagger** 或 **OpenAPI** 来描述 API，并生成文档。
- **Springdoc OpenAPI** 是帮助你自动生成符合 OpenAPI 3 规范的 API 文档的工具，尤其适合 Spring Boot 项目。
- 如果你希望文档界面更美观、更易用，可以使用 **Knife4j** 来增强 Swagger 的默认 UI。

### 举个例子

假设你在开发一个 Spring Boot 项目，里面有几个 RESTful API，比如获取用户信息、创建订单等。你想让这些 API 有文档可以展示，并且希望测试人员能够在文档页面中直接调用这些 API。

- 你可以使用 **Springdoc OpenAPI** 来生成符合 **OpenAPI 3** 标准的文档，它会自动扫描 Spring 注解，生成文档内容。
- 然后，Springdoc OpenAPI 可以集成 **Swagger UI**，提供一个交互式页面，方便用户调用 API。
- 如果你想要一个更好看、更易用的页面，可以集成 **Knife4j** 来替代默认的 Swagger UI，增强文档的视觉体验和交互功能。

### 总结

- **RESTful API** 是标准，描述如何设计你的接口。
- **Swagger 和 OpenAPI** 是描述 API 的标准和工具。
- **Springdoc OpenAPI** 是基于 OpenAPI 3 的 Spring Boot **集成库**，**用于自动生成文档。**
- **Knife4j** 是基于 Swagger 的增强 UI，用于更好地展示和交互 API 文档。

总结：**Springdoc OpenAPI是库，它可以自动生成 OpenAPI 文档；Swagger和knife4j是提供交互的ui界面，里面可以查看Springdoc OpenAPI通过注解生成的API文档，以及接口测试！！！**

![image-20241031103429789](.\assets\image-20241031103429789.png)

# SpringDoc OpenAPI常用注解

![image-20241031103609087](.\assets\image-20241031103609087.png)

# Knife4j 使用文档

## 介绍
Knife4j 是一个用于增强 Swagger UI 的工具，它可以在 Spring Boot 项目中轻松**集成并提供一个用户友好的界面**，用于显示和测试 API 文档。

## 工作原理

1. **集成**:
   - 在 Maven 或 Gradle 项目中**添加 Knife4j 的依赖**，通常是 `knife4j-spring-boot-starter`。

2. **自动配置**:
   - Knife4j 会在 Spring Boot 应用启动时**自动扫描并加载通过 Springdoc OpenAPI 生成的 API 文档。**

3. **文档展示**:
   - 当**访问特定的 URL 时，Knife4j 会呈现一个用户友好的界面**，显示所有 API 接口的文档信息，并支持交互测试。

## 示例代码

### 1. 添加依赖

在 `pom.xml` 中添加以下 Maven 依赖：
```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>版本号</version> <!-- 替换为最新版本 -->
</dependency>
```

### 2.创建配置类

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import org.springdoc.core.models.GroupedOpenApi;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Knife4jConfig {

    @Bean
    public GroupedOpenApi webApi() {
        return GroupedOpenApi.builder()
                .group("web-api")
                .pathsToMatch("/**")
                .build();
    }
    
    //功能: 创建一个名为 web-api 的 API 组。
    //pathsToMatch("/**"): 表示这个组将包括所有路径的 API 接口。
    //返回: 返回一个 GroupedOpenApi 对象，用于 Swagger 生成文档。  
    //可以理解为这个是分组的，请求路径参数为xxx的来web-api组

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
                .info(new Info()
                        .title("API文档")
                        .version("1.0")
                        .description("描述信息"));
    }
}

//功能: 自定义 API 文档的基本信息。
//OpenAPI: 用于配置 Swagger 文档的主对象。
//Info: 包含 API 的标题、版本和描述信息

```

### 3.访问文档

启动 Spring Boot 应用后，可以通过访问 `http://localhost:8080/doc.html`（具体路径视配置而定）来查看生成的 API 文档。



















