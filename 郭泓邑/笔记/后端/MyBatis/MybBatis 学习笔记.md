# MybBatis 学习笔记

# 一、MyBatis 是什么？

**MyBatis** 是一个优秀的持久层框架，本质是：

> 🔹 帮我们把 Java 对象 和 数据库 之间进行映射
>  🔹 解决 JDBC 中大量重复代码问题

中文版官网：
 https://mybatis.org/mybatis-3/zh_CN/index.html

------

# 二、MyBatis 核心流程

## 1️⃣ 基本执行流程

1. 加载核心配置文件（mybatis-config.xml）
2. 创建 SqlSessionFactory
3. 获取 SqlSession
4. 执行 Mapper 方法
5. 提交事务
6. 关闭资源

------

# 三、基础 CRUD 操作

假设数据库表：

```
CREATE TABLE user (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(50),
  age INT
);
```

对应实体类：

```
public class User {
    private Integer id;
    private String name;
    private Integer age;
}
```

------

## 1️⃣ 查询（Select）

### Mapper 接口

```
public interface UserMapper {
    List<User> selectAll();
}
```

### XML 映射文件

```
<select id="selectAll" resultType="com.demo.pojo.User">
    select * from user
</select>
```

------

## 2️⃣ 添加（Insert）

```
int insert(User user);
<insert id="insert">
    insert into user(name, age)
    values(#{name}, #{age})
</insert>
```

### ⚠ 参数说明

| 写法    | 含义                 |
| ------- | -------------------- |
| #{name} | 预编译占位符（推荐） |
| ${name} | 字符串拼接（不安全） |

------

## 3️⃣ 修改（Update）

```
int update(User user);
<update id="update">
    update user
    set name = #{name},
        age = #{age}
    where id = #{id}
</update>
```

------

## 4️⃣ 删除（Delete）

```
int deleteById(Integer id);
<delete id="deleteById">
    delete from user where id = #{id}
</delete>
```

------

# 四、XML 映射文件详解

一个标准的 Mapper XML 结构：

```
<mapper namespace="com.demo.mapper.UserMapper">

    <select></select>
    <insert></insert>
    <update></update>
    <delete></delete>

</mapper>
```

## namespace 的作用

必须和 Mapper 接口全限定名一致：

```
com.demo.mapper.UserMapper
```

否则会报错：

> Invalid bound statement (not found)

------

# 五、参数传递

## 1️⃣ 单个参数

```
User selectById(Integer id);
```

XML：

```
<select id="selectById" resultType="User">
    select * from user where id = #{id}
</select>
```

------

## 2️⃣ 多个参数

方式一：使用 @Param（推荐）

```
User selectByNameAndAge(@Param("name") String name,
                        @Param("age") Integer age);
```

XML：

```
<select id="selectByNameAndAge" resultType="User">
    select * from user
    where name = #{name}
    and age = #{age}
</select>
```

------

## 3️⃣ 对象参数

```
User select(User user);
<select id="select" resultType="User">
    select * from user
    where name = #{name}
</select>
```

------

# 六、resultType 与 resultMap

## 1️⃣ resultType（简单映射）

字段名和属性名一致时使用：

```
<select id="selectAll" resultType="User">
```

------

## 2️⃣ resultMap（自定义映射）

当字段名和属性名不一致时：

数据库：

```
user_name
```

实体类：

```
userName
```

### 解决方案：

```
<resultMap id="userMap" type="User">
    <id property="id" column="id"/>
    <result property="userName" column="user_name"/>
</resultMap>

<select id="selectAll" resultMap="userMap">
    select * from user
</select>
```

------

# 七、动态 SQL（重点）

动态 SQL 是 MyBatis 最大优势之一。

------

## 1️⃣ if 条件判断

```
<select id="selectByCondition" resultType="User">
    select * from user
    where 1=1
    <if test="name != null">
        and name = #{name}
    </if>
    <if test="age != null">
        and age = #{age}
    </if>
</select>
```

------

## 2️⃣ where 标签（推荐）

自动处理多余的 and

```
<select id="selectByCondition" resultType="User">
    select * from user
    <where>
        <if test="name != null">
            name = #{name}
        </if>
        <if test="age != null">
            and age = #{age}
        </if>
    </where>
</select>
```

------

## 3️⃣ set 标签（用于 update）

自动去除多余逗号

```
<update id="updateDynamic">
    update user
    <set>
        <if test="name != null">
            name = #{name},
        </if>
        <if test="age != null">
            age = #{age},
        </if>
    </set>
    where id = #{id}
</update>
```

------

## 4️⃣ foreach（批量操作）

### 批量删除

```
int deleteBatch(List<Integer> ids);
<delete id="deleteBatch">
    delete from user where id in
    <foreach collection="ids"
             item="id"
             open="("
             separator=","
             close=")">
        #{id}
    </foreach>
</delete>
```

------

## 5️⃣ choose（类似 switch）

```
<select id="selectChoose" resultType="User">
    select * from user
    <where>
        <choose>
            <when test="name != null">
                name = #{name}
            </when>
            <when test="age != null">
                age = #{age}
            </when>
            <otherwise>
                1 = 1
            </otherwise>
        </choose>
    </where>
</select>
```

------

# 八、MyBatis 优缺点

## ✅ 优点

- SQL 可控性强
- 支持动态 SQL
- 学习成本低
- 与 Spring 整合简单

## ❌ 缺点

- 需要写 XML
- SQL 与 Java 分离（维护成本）

------

# 九、与 SpringBoot 整合流程（简略）

依赖：

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
</dependency>
```

启动类添加：

```
@MapperScan("com.demo.mapper")
```

------

# 十、核心知识总结

| 模块      | 必须掌握                          |
| --------- | --------------------------------- |
| CRUD      | select / insert / update / delete |
| 参数传递  | 单参 / 多参 / 对象                |
| resultMap | 字段映射                          |
| 动态SQL   | if / where / set / foreach        |
| 批量操作  | foreach                           |

# 