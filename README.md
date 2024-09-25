[合集 \- 威哥爱编程(20\)](https://github.com)[1\.35个Redis企业级性能优化点与解决方案06\-25](https://github.com/wgjava/p/18267106)[2\.对比传统数据库，TiDB 强在哪？谈谈 TiDB 的适应场景和产品能力06\-25](https://github.com/wgjava/p/18267457)[3\.深度长文解析SpringWebFlux响应式框架15个核心组件源码07\-04](https://github.com/wgjava/p/18282994)[4\.Nginx性能调优5招35式不可不知的策略实战07\-08](https://github.com/wgjava/p/18289926)[5\.Java Executors类的9种创建线程池的方法及应用场景分析07\-09](https://github.com/wgjava/p/18292258)[6\.Redis数据结构—跳跃表 skiplist 实现源码分析07\-12](https://github.com/wgjava/p/18298064)[7\.Volatile不保证原子性及解决方案07\-19](https://github.com/wgjava/p/18311697)[8\.吃透 JVM 诊断方法与工具使用08\-01](https://github.com/wgjava/p/18336069)[9\.Java RMI技术详解与案例分析08\-05](https://github.com/wgjava/p/18343209)[10\.通过JUnit源码分析学习编程的奇技淫巧08\-12](https://github.com/wgjava/p/18354483):[豆荚加速器](https://yirou.org)[11\.什么是依赖倒置原则08\-14](https://github.com/wgjava/p/18359195)[12\.初探 Rust 语言与环境搭建08\-19](https://github.com/wgjava/p/18366810)[13\.为什么用Vite框架？来看它的核心组件案例详解08\-22](https://github.com/wgjava/p/18373550)[14\.Vue状态管理库Pinia详解08\-23](https://github.com/wgjava/p/18375597)[15\.Tomcat的配置文件中有哪些关键的配置项，它们分别有什么作用？08\-26](https://github.com/wgjava/p/18381701)[16\.ECharts实现雷达图详解09\-02](https://github.com/wgjava/p/18392833)[17\.OpenFeign深入学习笔记09\-03](https://github.com/wgjava/p/18394262)[18\.阿里面试让聊一聊Redis 的内存淘汰（驱逐）策略09\-23](https://github.com/wgjava/p/18427219)[19\.除了递归算法，要如何优化实现文件搜索功能09\-24](https://github.com/wgjava/p/18428801)20\.关于建表字段是否该使用not null这个问题你怎么看?09\-25收起
大家好，我是 V 哥，在数据库设计中，是否使用 `NOT NULL` 是一个非常重要的决策，直接影响数据完整性、查询性能以及业务逻辑的复杂度。使用 `NOT NULL` 的关键在于理解业务需求和具体场景。


下面V哥通过一些场景来分析什么时候应该使用 `NOT NULL`，什么时候允许 `NULL`。一起聊聊经验之谈，望和兄弟们讨论。


## 1\. 必须存在值的字段


对于某些关键字段，如果业务逻辑要求它们始终具有值，那么应该使用 `NOT NULL` 约束。这样可以防止数据不完整，避免潜在的业务问题。


### 示例：用户注册场景


* **字段**：`username`、`email`
* **分析**：用户在注册时，用户名和邮箱是必须的。缺少这两个字段会导致后续的登录或通知无法进行，因此这些字段应该设置为 `NOT NULL`。



```
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL
);

```

### 影响：


* 数据完整性：确保任何用户都具备用户名和邮箱。
* 性能优化：`NOT NULL` 字段可以优化数据库的索引和查询效率。


## 2\. 可选字段


有些字段是可选的，它们的缺失不会对业务逻辑造成影响。在这种情况下，允许 `NULL` 可以为业务提供灵活性，避免强制要求用户提供所有信息。


### 示例：用户信息场景


* **字段**：`middle_name`、`profile_picture`
* **分析**：中间名和个人资料图片通常是可选信息。如果强制使用 `NOT NULL`，则需要提供默认值，这在某些情况下不太合理。比如用户不提供个人资料图片，字段可以设置为 `NULL`。



```
CREATE TABLE user_profiles (
    id INT PRIMARY KEY,
    middle_name VARCHAR(255),
    profile_picture VARCHAR(255)
);

```

### 影响：


* 灵活性：允许字段为空为用户提供更多自由，同时保持数据结构的灵活性。
* 业务适应性：随着业务需求的变化，允许 `NULL` 的字段可以容纳更多的边缘情况。


## 3\. 需要标识未知状态的字段


在某些业务场景中，`NULL` 可以表示“未知”或“未提供”的状态，而不仅仅是“空值”。这种情况下，允许 `NULL` 是合理的，因为它能明确区分“没有值”和“值为空”。


### 示例：订单处理场景


* **字段**：`shipped_date`
* **分析**：订单的发货日期在创建时可能未知，只有在订单发货后才会填入。如果使用 `NULL` 表示未发货状态，可以简化业务逻辑，并且更符合直观的业务需求。



```
CREATE TABLE orders (
    id INT PRIMARY KEY,
    order_date DATE NOT NULL,
    shipped_date DATE
);

```

### 影响：


* 状态表达：`NULL` 可以清晰地表示某个状态未发生，如订单尚未发货。
* 简化业务逻辑：不用添加额外的布尔字段来表示是否发货。


## 4\. 外键字段和 `NOT NULL`


外键的设计中，是否使用 `NOT NULL` 依赖于业务逻辑。强制 `NOT NULL` 意味着关联关系是强制性的；允许 `NULL` 则表示某些记录可能暂时没有关联项。


### 示例：博客文章和作者


* **字段**：`author_id`
* **分析**：如果业务逻辑允许某些文章没有具体作者（如系统自动生成），那么 `author_id` 字段可以允许 `NULL`。如果每篇文章都必须有作者，则 `NOT NULL` 更为合理。



```
CREATE TABLE posts (
    id INT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    author_id INT REFERENCES users(id)  -- 可以是 NULL
);

```

### 影响：


* 强制关联关系：使用 `NOT NULL` 可以确保数据的完整性和一致性。
* 灵活关联：允许 `NULL` 的外键字段为业务提供更多灵活性，支持特殊场景。


## 5\. 性能与存储开销


从性能角度来看，`NOT NULL` 字段在某些情况下可以加速查询。因为数据库可以更有效地处理不允许 `NULL` 的字段，不需要对 `NULL` 值进行额外的判断。然而，如果太多字段都设置为 `NOT NULL`，则可能导致业务复杂性增加。


### 示例：高频查询字段


* **字段**：`last_login`
* **分析**：对于用户的登录系统，查询最后登录时间是常见操作。如果该字段被设置为 `NOT NULL`，数据库可以更快地检索数据。



```
CREATE TABLE user_sessions (
    id INT PRIMARY KEY,
    user_id INT NOT NULL,
    last_login TIMESTAMP NOT NULL
);

```

是否使用 `NOT NULL` 应根据业务需求来决定。对于**关键字段**（如用户名、订单 ID 等），`NOT NULL` 可以保证数据完整性。而对于**可选字段**或表示状态的字段（如发货日期、可选信息等），允许 `NULL` 可能会提供更大的灵活性。


## 6\. 版本管理和演化中的数据库设计


在长期的项目中，数据库架构会随着业务需求的变化而演化。有时，允许 `NULL` 可以为将来未预见的扩展提供灵活性。


### 示例：产品升级和新功能场景


* **字段**：`discount_rate`
* **分析**：假设在初期设计中，所有产品都没有折扣，因此 `discount_rate` 字段在数据库设计时不需要。但是随着业务的扩展，某些产品开始引入折扣机制。在这种情况下，最初的产品可能没有折扣值，因此可以让 `discount_rate` 允许为 `NULL`，表示未使用折扣。



```
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    discount_rate DECIMAL(5, 2)  -- 允许为 NULL，表示无折扣
);

```

### 影响：


* 演化灵活性：允许 `NULL` 为未来的扩展提供了更多空间，避免了在设计初期过度限制。
* 代码维护：开发团队可以根据业务需求逐步添加新功能，而不必在每次迭代时重新设计表结构。


## 7\. 基于数据统计和分析的设计


在一些数据统计场景中，允许字段为 `NULL` 可以提供更清晰的数据视图，尤其是针对数据缺失的处理。`NULL` 明确表示数据不存在，而非无意义的默认值。


### 示例：用户活动追踪


* **字段**：`last_purchase_date`
* **分析**：在用户行为分析中，追踪用户最后一次购买的日期是常见需求。如果用户从未购买过，`last_purchase_date` 可以为 `NULL`。使用 `NULL` 而不是使用虚假的默认值（如 '1970\-01\-01'）能更准确地反映业务状态。



```
CREATE TABLE user_activity (
    user_id INT PRIMARY KEY,
    last_login DATE NOT NULL,
    last_purchase_date DATE  -- 可以为 NULL，表示没有购买记录
);

```

### 影响：


* 数据准确性：`NULL` 比默认值更好地表达“未发生”这一状态，避免使用错误数据进行分析。
* 分析效率：通过 `IS NULL` 判断可以快速筛选出未进行过某些行为的用户。


## 8\. 迁移和数据兼容性


在进行数据库迁移或者不同系统之间的数据整合时，允许 `NULL` 通常能提升兼容性，尤其在早期设计和目标系统不一致的情况下。如果源系统的数据允许某些字段为空，而目标系统不允许 `NULL`，那么迁移过程中可能会遇到问题。


### 示例：跨系统数据迁移


* **字段**：`address_line_2`
* **分析**：假设一个系统将用户地址划分为 `address_line_1` 和 `address_line_2`，其中 `address_line_2` 是可选的。而在目标系统中，`address_line_2` 使用 `NOT NULL` 会导致部分数据迁移失败。因此，在这种情况下，设计时应允许 `NULL`，确保数据兼容性。



```
CREATE TABLE user_addresses (
    user_id INT PRIMARY KEY,
    address_line_1 VARCHAR(255) NOT NULL,
    address_line_2 VARCHAR(255)  -- 允许 NULL，因为不是所有用户都需要填写
);

```

### 影响：


* 迁移成功率：允许 `NULL` 可以提高数据迁移的兼容性，确保不同系统的数据能够无缝整合。
* 数据映射：避免强制使用默认值或虚假数据来填补空缺，提高数据的准确性。


## 9\. 使用 `NOT NULL` 和默认值的结合


在一些情况下，使用 `NOT NULL` 并搭配**默认值**可以提高字段的健壮性，避免开发人员在插入数据时遗漏某些信息。例如，对于布尔类型字段或者枚举类型字段，通常通过 `NOT NULL` 和默认值确保逻辑上的完整性。


### 示例：订单状态管理


* **字段**：`order_status`
* **分析**：在订单管理系统中，订单状态是必不可少的字段。为了确保每个订单在创建时都具有明确的状态，使用 `NOT NULL` 并设定默认值（如 "Pending"）可以避免遗漏。



```
CREATE TABLE orders (
    id INT PRIMARY KEY,
    order_date DATE NOT NULL,
    order_status VARCHAR(50) NOT NULL DEFAULT 'Pending'
);

```

### 影响：


* 业务健壮性：通过 `NOT NULL` 和默认值结合，确保业务中的每个流程都具备初始状态，避免逻辑错误。
* 易于维护：默认值减少了数据插入时的复杂性，确保系统一致性。


## 10\. 动态数据结构和 `JSON` 类型


在现代数据库设计中，使用 `JSON` 类型存储不规则或动态数据的情况越来越常见。对于这种场景，是否使用 `NOT NULL` 的决策与传统的关系型字段设计不同。在大部分情况下，`JSON` 字段是灵活的，可为空，以适应多样化的数据格式。


### 示例：用户偏好设置


* **字段**：`preferences`
* **分析**：假设我们需要存储用户偏好设置，但不同用户的偏好种类和数量都不一致。使用 `JSON` 类型可以灵活存储这些信息。允许 `NULL` 可以处理未提供偏好的用户数据。



```
CREATE TABLE user_settings (
    user_id INT PRIMARY KEY,
    preferences JSON  -- 允许 NULL 表示用户未设置偏好
);

```

### 影响：


* 灵活性：使用 `NULL` 和 `JSON` 类型的组合，可以处理动态且不规则的数据结构，适应不同用户的需求。
* 可扩展性：在后续的需求变化中，不必修改表结构，直接通过更新 `JSON` 数据即可。


## 总结一下：如何平衡 `NOT NULL` 与 `NULL`


在决定是否使用 `NOT NULL` 时，应该考虑以下几点：


* **业务需求**：关键业务逻辑需要强制值的字段应使用 `NOT NULL`，而可选项和边缘情况允许 `NULL`。
* **数据准确性**：`NULL` 能够更好地表达“未知”或“未发生”的状态，而非使用无意义的默认值。
* **性能与维护性**：`NOT NULL` 可以优化查询性能，减少数据库索引负担，但在允许 `NULL` 的情况下，灵活性和兼容性会更高。
* **未来的扩展**：在设计初期，应考虑到未来业务的演化，过早限制字段为 `NOT NULL` 可能会在扩展时带来挑战。
* 使用 `NOT NULL` 的场景：关键业务字段、数据一致性要求高、频繁查询的字段。
* 允许 `NULL` 的场景：可选字段、表示未知状态、灵活关联关系。


所以呢，根据业务场景合理使用 `NOT NULL`，可以在保持数据完整性的同时提供必要的灵活性和性能优化。数据库设计的时候，我们可以在灵活性、性能和数据完整性之间找到平衡。


