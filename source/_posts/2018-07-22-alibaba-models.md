---
layout: post
title: "数据模型 - 阿里规约（1）"
category: "spring"
tags: ['java', 'spring']
---


在阿里巴巴编码规约中列举了下面几个领域模型规约: 

* **DO（Data Object）**：与数据库表结构一一对应，通过 DAO 层向上传输数据源对象。也常叫做 Entity。 
* **DTO（Data Transfer Object）**：数据传输对象，Service 或 Manager 向外传输的对象。 
* **BO（Business Object）**：业务对象。由 Service 层输出的封装业务逻辑的对象。 
* **AO（Application Object）**：应用对象。在 Web 层与 Service 层之间抽象的复用对象模型，极为贴近展示层，复用度不高。 
* **VO（View Object）**：显示层对象，通常是 Web 向模板渲染引擎层传输的对象。注意与另一类 VO（Value Object）不一样<sup>[1]</sup>，后者常被用作枚举。
* **Query**：数据查询对象，各层接收上层的查询请求。注意超过2个参数的查询封装，禁止使用 Map 类来传输。

### 使用经验

根据项目复杂度的不同，我们不一定实现所有层的模型对象，根据必要性从大到小，我认为应该是这个顺序：DO > DTO > VO > Query > AO、BO

<!-- more -->

#### 单独使用 DO

DO 映射了数据表结构，必须要定义。当系统极简单时，可以只用 DO 一层模型，返回到 view 层或接口层，但是缺点也很直观：

* 对敏感数据的封装处理复杂，比如 user.password
* 对接口多样化需要处理复杂，比如接口 A 需要返回 10 个字段，接口 B 需要返回同一模型的 5 个字段，一般还需要将结果再次处理，转换成其它数据结构（例如 Map）再返回

#### DO、DTO 组合

当系统稍微复杂时，就需要在 Controller 和 Service，以及 Service 层之间传递模型对象，尤其是跨 JVM，跨网络传输时，比如 UserService 和 MessageService 不部署在同一主机上，这时就要加入 DTO 模型，它的特点是：

* DTO 一般继承 `java.io.Serializable`，为了跨 JVM，跨网络传输

#### DO、DTO、VO 组合

当系统继续扩展，我们会遇到同一底层数据（DO、DTO）需要不同的呈现结果。比如搜索商品，对于返回的 Product 模型，页面模板 A 需要 20 个属性；页面模板 B 需要 10 个属性，但这 10 个属性并不完全包含在 A 需要的 20 个属性内；接口 C 是动态下拉列表，只需要 id, name 两个属性。对于这样的需求，一种模型是不能满足的，每个业务都去通过 Map 转换虽然可能满足需求，但重复的转换代码和 Map 属性的不可控性，都为后期的维护造成困难。

更好的解决方案是定义 VO 层模型，对应视图层不同的需求，它的优点是：

* 相对于 Map，保证属性完整
* 模型能统一转换，后期可维护性提高（参见 ModelMapper<sup>[2]</sup>）
* 可以根据视图要求，扩展复杂的属性

**扩展属性**

```java
Class UserVO {
  String firstName { get; set; }
  String lastName { get; set; }

  // 扩展属性：依靠其它属性或一些复杂计算
  String fullName;

  String getFullName (){
    return this.firstName + this.lastName;
  }
}
```

**ModelMapper 转换**

```java
Class UserDTO {
  Integer id { get; set; }
  String firstName { get; set; }
  String lastName { get; set; }
  String email { get; set; }
  String password { get; set; }
}

Class UserVO {
  String firstName { get; set; }
  String lastName { get; set; }
}

User user = new UserDTO();
user.setId(1L);
user.setFirstName("三");
user.setLastName("张");
user.setEmail("101@qq.com");
user.setPassword("a1b2c3");
// 转换：DTO -> VO
ModelMapper modelMapper = new ModelMapper();
UserVO userVO = modelMapper.map(user, UserVO.class);
// 显示结果
System.out.println(userVO);
```

### 项目存在的问题

实际上，目前的项目中混用了 DO 和 DTO，既映射数据表对象，又在子系统中传递。另外，我们应该增加 VO 模型来替代之前返回 Map 的情况。

**遗留问题**：多个 VO 命名规则，以及对系统带来的复杂性？

### 参考：

1. [Difference between DTO, VO, POJO, JavaBeans?](https://stackoverflow.com/questions/1612334/difference-between-dto-vo-pojo-javabeans)
2. [ModelMapper](https://github.com/modelmapper/modelmapper)
