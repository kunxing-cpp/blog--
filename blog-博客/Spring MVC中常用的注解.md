# Spring MVC中常用的注解：

### 一,    常见定义控制器的注解：

    1 .**`@Component`**

    2 .**`@Configuration`**

    3 .**`@Controller`**

    4 .**`@Service`**

    5 .**`@Repository`**

****

## **重点：**

#### **`@Component`** 注解

#### 1，概念：

     `@Component`是Spring框架中**最核心的注解之一**，用于标识一个类为Spring容器管理的组件。通过 **组件扫描机制** ，Spring会自动实例化这些类并将其纳入[IoC容器](https://zhida.zhihu.com/search?content_id=254836015&content_type=Article&match_order=1&q=IoC%E5%AE%B9%E5%99%A8&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTg0NTcxNTIsInEiOiJJb0PlrrnlmagiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNTQ4MzYwMTUsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.-QaHBj_zYKEisyG3oANBk7FaHyDPQsC_j1TRHEOvc40&zhida_source=entity)进行管理，是实现 **依赖注入（DI)** 和 **控制反转（IoC)** 的重要基础。

#### 2，实现原理：

  **a,  组件扫描机制:**

            通过`@ComponentScan`注解或在XML中配置`<context:component-scan>`启用

   Spring启动时扫描指定包路径下带有`@Component`及其派生注解的类

   **b, 注册Bean过程：**

             **类路径扫描**：查找所有符合条件的.class文件

             **解析注解**：识别带有`@Component`的类

             **创建[BeanDefinition](https://zhida.zhihu.com/search?content_id=254836015&content_type=Article&match_order=1&q=BeanDefinition&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTg0NTcxNTIsInEiOiJCZWFuRGVmaW5pdGlvbiIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI1NDgzNjAxNSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.wwpVOwg6ODwWJNUdSmv1X9gL3dvDbSRTYi3UakfwP_Q&zhida_source=entity)** ：将类信息存入Bean定义注册表

             **实例化Bean**：通过反射创建对象并处理依赖注入

             **生命周期管理**：处理`@PostConstruct`、`@PreDestroy`等回调

  **c, 派生注解:**

1. `@Service：业务逻辑层`

2. `@Repository：数据访问层（自动转换持久层异常）`

3. `@Controller：Web控制器层`

4. `@Configuration：配置类 `

> **作用：** 以上注解继承了父注解 **@Component** ，@Component 的作用是把普通**pojo实例化**到spring容器中，相当于配置文件中的，Spring会扫描加了@RestController、@Service、@Controller、@Repository、@Component注解的类，并把这些类纳入进spring容器中管理。

****

## 二，前后端传参常用注解：

### 1，它们分别用于处理不同来源的请求数据：

1. **`@RequestParam`**

2. **`@PathVariable`**

3. **`@RequestBody`**

4. **`@RequestHeader`**

5. **`@ModelAttribute`**

****

### 2，流程图：

 为了更直观地理解它们的区别和应用场景，可以参考下面的流程图，它展示了一个HTTP请求到达Spring MVC后的**参数绑定过程**

<img title="" src="file:///C:/Users/33313/Pictures/deepseek_mermaid_20250919_dadaf0.svg" alt="" width="717">

****

### 3，各个注解的原理与作用：

#### 1. @RequestParam

- **作用**：用于**获取 URL 查询字符串（Query String）** 的值或**表单（`application/x-www-form-urlencoded`）** 提交的数据。

- **数据来源**：`http://example.com/api?name=张三&age=20` 中的 `name=张三&age=20` 部分。

- **原理**：Spring MVC 会从 `ServletRequest` 对象中获取参数映射（Parameter Map），并根据注解指定的参数名（或默认使用方法参数名）将值提取出来，进行类型转换（如 String 到 Integer）后注入到方法参数中。

- **使用场景**：
  
  - 处理 GET 请求的查询参数。
  
  - 处理 POST 提交的表单数据（Content-Type: `application/x-www-form-urlencoded`）。

### 2. @PathVariable

- **作用**：用于**获取 URL 路径模板（URI Template）** 中的变量值，是构建 RESTful API 的核心注解。

- **数据来源**：URL 路径中的一部分，如 `/api/users/123` 中的 `123`。

- **原理**：在请求匹配路由时（如 `@GetMapping("/users/{id}")`），Spring MVC 会将 URL 中的模板变量 `{id}` 解析出来，并将其值转换后注入到使用 `@PathVariable` 注解的方法参数中。

- **使用场景**：
  
  - RESTful 风格 API，通过 URL 路径标识资源。例如，获取 ID 为 123 的用户：`GET /users/123`。

### 3. @RequestBody

- **作用**：用于将 **HTTP 请求体（Body）** 中的数据（通常是 JSON 或 XML）**绑定（反序列化）** 到方法的参数对象上。

- **数据来源**：请求体（Request Body）中的原始数据。

- **原理**：Spring MVC 通过 `HttpMessageConverter` 接口来转换 HTTP 请求和响应消息。当方法参数使用 `@RequestBody` 时，Spring 会根据请求的 `Content-Type`（如 `application/json`）选择适当的转换器（如 `MappingJackson2HttpMessageConverter`），将请求体中的 JSON 字符串转换为指定的 Java 对象。

- **使用场景**：
  
  - 处理 POST/PUT 请求中提交的 JSON 或 XML 数据，常见于前后端分离架构中。
  
  - 创建或更新资源时，接收前端传递的复杂对象。

### 4. @RequestHeader

- **作用**：用于将**请求头（Request Header）** 中的数据绑定到方法参数。

- **数据来源**：HTTP 请求头信息。

- **原理**：Spring MVC 从 `HttpServletRequest` 对象中获取指定的 Header 值，并进行类型转换后注入。

- **使用场景**：
  
  - 需要获取用户代理（User-Agent）、认证信息（Authorization）、内容类型（Content-Type）等头信息时。

### 5. @ModelAttribute

- **作用**：
  
  1. **用于方法参数**：从模型（Model）中获取属性，或者将表单数据绑定到对象（同时将对象暴露给模型）。它支持数据绑定和验证。
  
  2. **用于方法**：表示该方法会在控制器中其他请求处理方法之前执行，用于准备模型数据（如下拉菜单的选项列表）。

- **数据来源**：通常来源于表单提交（`application/x-www-form-urlencoded`），也可以是模型中的属性。

- **原理**：当用于参数时，Spring MVC 会查看模型属性中是否有同名对象，如果没有，则实例化一个。然后尝试将请求参数按名称匹配到该对象的属性上（数据绑定）。如果绑定过程中发生错误，这些错误会被收集到 `BindingResult` 对象中。

- **使用场景**：
  
  - 主要用于传统的非前后端分离的 Web 开发（如使用 JSP、Thymeleaf 渲染视图）。
  
  - 处理包含多个字段的表单提交，并希望自动将这些字段填充到一个命令对象（Command Object）中。

****

### 4，总结对比表

| 注解                    | 作用              | 数据来源           | 常见 Content-Type                       | 主要应用场景           |
| --------------------- | --------------- | -------------- | ------------------------------------- | ---------------- |
| **`@RequestParam`**   | 获取查询参数或表单字段     | URL 查询字符串、表单   | `application/x-www-form-urlencoded`   | GET 参数、简单表单      |
| **`@PathVariable`**   | 获取 URL 路径中的变量   | URL 路径片段       | 任意                                    | RESTful 资源标识     |
| **`@RequestBody`**    | **绑定请求体**为对象    | **请求体 (Body)** | `application/json`, `application/xml` | **前后端分离，接收JSON** |
| **`@RequestHeader`**  | 获取请求头信息         | HTTP 请求头       | 任意                                    | 获取特定Header       |
| **`@ModelAttribute`** | 绑定表单数据到对象并暴露给模型 | 表单数据、模型属性      | `application/x-www-form-urlencoded`   | 传统MVC表单处理        |

**核心选择原则：**

- **前端发的是 JSON？** -> **用 `@RequestBody`**

- **参数在 URL 的 `?` 后面？** -> **用 `@RequestParam`**

- **参数是 URL 路径的一部分（如 `/users/1`）？** -> **用 `@PathVariable`**

- **需要获取认证头、客户端信息？** -> **用 `@RequestHeader`**

- **在做传统多页应用，需要渲染视图？** -> **考虑用 `@ModelAttribute`**
