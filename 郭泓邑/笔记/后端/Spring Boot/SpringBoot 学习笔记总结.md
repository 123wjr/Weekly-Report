# SpringBoot 学习笔记总结

------

# 一、请求与响应（Web 核心）

------

## 1️⃣ 请求响应概述

### 🌟 本质

前后端交互模式：

```
浏览器（Request） → 服务器（Controller处理） → 返回数据（Response）
```

### 流程

```
客户端 → DispatcherServlet → Controller → Service → Dao → 数据库
                                      ↓
                                   返回JSON
```

------

## 2️⃣ Postman 工具

### 作用

用于：
 ✅ 测试接口
 ✅ 模拟前端请求
 ✅ 调试后端API

### 常用功能

| 功能        | 说明         |
| ----------- | ------------ |
| GET         | 查询数据     |
| POST        | 新增数据     |
| PUT         | 修改数据     |
| DELETE      | 删除数据     |
| Body → JSON | 发送JSON参数 |

------

------

# 二、请求参数接收（重点高频）

------

## 3️⃣ 简单参数 & 实体参数

------

### ✅ 简单参数（基本类型）

### 前端：

```
/user?id=1&name=Tom
```

### 后端：

```
@GetMapping("/user")
public String test(Integer id, String name) {
    return id + name;
}
```

📌 特点：

- 参数名必须一致
- 自动封装

------

### ✅ 实体参数（对象封装 ⭐推荐）

### 前端：

```
{
  "id": 1,
  "name": "Tom"
}
```

### 后端：

```
@PostMapping("/user")
public String add(@RequestBody User user){
    return user.toString();
}
```

📌 优点：

- 清晰
- 易扩展
- 企业开发首选

------

------

## 4️⃣ 数组 / 集合参数

------

### 数组

```
?id=1&id=2&id=3
public String test(Integer[] ids)
```

------

### 集合

```
public String test(List<Integer> ids)
```

------

------

## 5️⃣ 日期参数 & JSON参数

------

### 日期

```
@DateTimeFormat(pattern = "yyyy-MM-dd")
Date birthday
```

------

### JSON（必须用 @RequestBody）

```
@PostMapping
public void add(@RequestBody User user)
```

📌 注意：
 ❌ 不加 @RequestBody 接收不到 JSON

------

------

## 6️⃣ 路径参数（REST风格 ⭐⭐⭐）

------

### 请求：

```
/user/10
```

### 接收：

```
@GetMapping("/user/{id}")
public String test(@PathVariable Integer id)
```

📌 优点：

- 更规范
- 更符合 RESTful
- 企业常用

------

------

# 三、响应数据

------

## 7️⃣ @ResponseBody

### 作用

把 Java对象 → JSON 返回

------

### 使用方式

### ① 方法上

```
@ResponseBody
@GetMapping("/user")
public User get(){
    return new User();
}
```

### ② 类上（推荐 ⭐）

```
@RestController
```

=

```
@Controller + @ResponseBody
```

------

------

## 8️⃣ 响应案例（标准写法）

### 统一返回格式 ⭐企业规范

```
public class Result {
    private Integer code;
    private String msg;
    private Object data;
}
```

### 返回：

```
return Result.success(user);
```

------

------

# 四、分层解耦（三层架构 ⭐⭐⭐⭐⭐ 面试必考）

------

## 9️⃣ 三层架构

------

### 结构图

```
Controller  控制层（接收请求）
    ↓
Service     业务层（业务逻辑）
    ↓
Dao/Mapper  持久层（数据库）
```

------

## 各层职责

| 层         | 作用               |
| ---------- | ------------------ |
| Controller | 接收请求、返回数据 |
| Service    | 业务处理           |
| Dao        | 操作数据库         |

------

## 优点

✅ 职责清晰
 ✅ 易维护
 ✅ 易扩展
 ✅ 解耦

------

------

# 五、IOC & DI（Spring 核心思想 ⭐⭐⭐⭐⭐）

------

## 🔟 IOC（控制反转）

### 传统：

```
UserService service = new UserService();
```

### IOC：

👉 对象由 Spring 创建

```
@Autowired
UserService service;
```

📌 思想：

> 不自己 new，对象交给 Spring 管理

------

------

## 11️⃣ DI（依赖注入）

### 含义

Spring 自动帮你注入依赖对象

------

### 三种方式

### ① 属性注入

```
@Autowired
private UserService service;
```

------

### ② 构造注入 ⭐推荐

```
private final UserService service;

public UserController(UserService service){
    this.service = service;
}
```

------

### ③ set方法注入

```
@Autowired
public void setService(UserService service){}
```

------

------

# 六、IOC + DI 优点总结

| 优点   | 说明           |
| ------ | -------------- |
| 解耦   | 降低类之间依赖 |
| 易测试 | 方便Mock       |
| 易扩展 | 替换实现简单   |
| 易维护 | 结构清晰       |

------

------

# 🎯 高频面试题总结

------

### Q1：@RestController 和 @Controller 区别？

```
@RestController = @Controller + @ResponseBody
```

------

### Q2：@RequestBody 作用？

接收 JSON 数据

------

### Q3：三层架构优点？

解耦、分工明确、可维护

------

### Q4：IOC 是什么？

对象由 Spring 创建

------

### Q5：DI 是什么？

Spring 自动注入依赖