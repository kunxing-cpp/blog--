## 一，简要概述：

**1，DTO（数据传输对象）：用于接收前端请求参数**

**2 ,  VO（视图对象）：返回给前端的最终数据**

**3，entity(实体表) ： 对应数据库表**

****

## 二，DTO与VO的原理：

隔离层与层之间的数据传递，避免暴露内部实现，然后说明具体用法，在不同软件层次之间传输数据时，使用专门定制的数据对象，而不是使用内部的、原始的数据模型（Entity），从而实现各层之间的解耦和职责分离。

****

### 三，如何使用DTO与VO：

我们以《苍穹外卖》中“查询订单详情”这个场景为例，展示DTO和VO的完整使用流程。下图清晰地展示了数据在各层之间的转换过程：

![](C:\Users\33313\AppData\Roaming\marktext\images\2025-09-17-20-46-47-image.png)



#### 在代码各层中的使用：

**Controller层：接收DTO，返回VO**

    @RestController
    @RequestMapping("/order")
    public class OrderController
     {
            @Autowired
            private OrderService orderService;
    
            // 查询订单详情
            @GetMapping("/detail")
            public Result<OrderDetailVO> detail(OrderQueryDTO orderQueryDTO)
            { 
                // 入参是DTO
                // 1. 校验参数（如@NotNull注解已通过校验框架处理）
                // 2. 调用Service层，传入DTO
                OrderDetailVO orderDetailVO = orderService.getDetailById(orderQueryDTO);
                // 3. 将VO返回给前端
                return Result.success(orderDetailVO);
            }
    }

**Service层：处理DTO，组装VO**

    @Service
    public class OrderServiceImpl implements OrderService
    {
            @Autowired
            private OrderMapper orderMapper;
    
            @Autowired
            private UserService userService;
    
            @Override
            public OrderDetailVO getDetailById(OrderQueryDTO dto)
            {
            // 1. 使用从DTO中获取的ID查询数据库，得到Entity
            Order order = orderMapper.selectById(dto.getOrderId());
    
            // 2. 进行各种业务逻辑处理...
    
            // 3. 将Entity和其他数据组装成VO（核心步骤）
            OrderDetailVO vo = new OrderDetailVO();
            // 使用BeanUtils等工具复制基础属性
            BeanUtils.copyProperties(order, vo); 
            // 设置需要转换的字段
            vo.setStatusDesc(OrderStatusEnum.getDescByStatus(order.getStatus())); 
            // 调用其他服务，组装复杂数据
            vo.setUserInfo(userService.getUserSimpleInfo(order.getUserId())); 
            vo.setDishList(orderDishService.listDishByOrderId(order.getId()));
    
            // 4. 返回VO
            return vo;
            }
    }

### 总结：

| 对象         | 目的           | 所处层次              | 关键特征          |
| ---------- | ------------ | ----------------- | ------------- |
| **Entity** | 与数据库映射       | Dao / Mapper 层    | 与表结构一致，包含所有字段 |
| **DTO**    | **接收**前端请求参数 | **Controller 入参** | 定制化接收参数，便于校验  |
| **VO**     | **返回**给前端的数据 | **Controller 出参** | 定制化输出数据，高度灵活  |

**使用它们的根本理由是：构建一个职责清晰、安全稳定、易于维护的分层架构**





### 四，DTO与VO的使用场景：

| 情况描述                     | 是否需要？      | 解释                        |
| ------------------------ | ---------- | ------------------------- |
| **API返回的数据 ≠ 数据库实体**     | **需要 VO**  | 用于定制化、格式化返回给前端的数据。        |
| **API请求参数 ≠ 数据库实体**      | **需要 DTO** | 用于封装复杂的、来自前端的请求参数。        |
| **需要隐藏内部实现细节（如密码）**      | **需要 VO**  | 保证数据安全性，防止敏感信息泄露。         |
| **需要对入参进行校验（如@NotNull）** | **需要 DTO** | 避免将校验注解污染数据实体（Entity）。    |
| **微服务之间的接口调用**           | **需要 DTO** | 作为服务间的数据契约，实现解耦。          |
| **简单的个人项目/快速原型**         | **可不用**    | 追求开发速度，可直接用Entity，但要知其风险。 |
| **正式的企业级/多人协作项目**        | **必须用**    | 保证代码清晰、可维护、可扩展的核心实践。      |

****

### 五、有什么好处？（为什么这么做？）

#### 1. 安全性（最重要！）

- **避免暴露数据库结构**：Entity通常包含大量敏感字段（如`password`、`deleted`逻辑删除标记、关联ID等），直接返回给前端会造成信息泄露。

- **VO可以完全控制输出的数据**，只暴露前端需要的那部分。

#### 2. 解耦与稳定性

- **各层独立变化**：数据库表结构（Entity）变更时，只要保证DTO和VO的接口不变，**Controller层的代码就不需要修改**，不影响前端。反之，前端需要增加新字段时，只需在VO中添加，无需改动Entity和数据库（至少短期内）。

- **清晰的职责划分**：Entity代表数据库，DTO代表API入参，VO代表API出参。各司其职，代码意图更清晰。

#### 3. 定制化与灵活性

- **减少不必要的数据传输**：例如，订单列表接口可能只需要`id`、`number`、`status`几个字段，而详情接口需要几十个字段。你可以定义`OrderListVO`和`OrderDetailVO`来分别应对，**避免总是查询和传输所有字段**，提升性能。

- **适配不同客户端**：PC端和App端需要的的数据结构可能不同，可以为它们创建不同的VO，而后台业务逻辑（Service）和数据库（Entity）可以保持不变。

#### 4. 可维护性

- **参数校验更清晰**：可以在DTO的字段上直接使用注解（如`@NotNull`, `@Email`）进行校验，校验逻辑离入口（Controller）最近，非常自然。

- **代码可读性更强**：看到`OrderQueryDTO`就知道它是查询订单的入参；看到`OrderDetailVO`就知道它是返回给前端的订单详情。而不是在所有地方都使用庞大的`Order`对象。
