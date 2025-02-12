# [MyBatis-Plus 查询条件组装的多种实现方式](https://github.com/humyna/gitblog/issues/33)

在使用 MyBatis-Plus 进行开发时，查询条件的组装是一个常见且重要的操作。MyBatis-Plus 提供了多种方式来实现这一功能，以满足不同场景下的需求。本文将介绍几种常见的查询条件组装方式，并探讨为什么 MyBatis-Plus 会提供这些不同的方法。

#### 1. 使用 `QueryWrapper`

`QueryWrapper` 是 MyBatis-Plus 提供的一个用于构建动态查询条件的工具类。它支持链式调用，能够方便地组合各种查询条件。

**示例：**

```java
@Autowired
private UserMapper userMapper;

public List<User> getUsers(String name, Integer age) {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    
    if (name != null && !name.isEmpty()) {
        queryWrapper.eq("name", name);
    }
    
    if (age != null) {
        queryWrapper.eq("age", age);
    }
    
    return userMapper.selectList(queryWrapper);
}
```

这种方式直观易懂，适合大多数简单查询场景。

#### 2. 使用 `LambdaQueryWrapper`

`LambdaQueryWrapper` 是 `QueryWrapper` 的 lambda 表达式版本，提供了类型安全和编译时检查，避免了手动拼接字段名可能带来的错误。

**示例：**

```java
@Autowired
private UserMapper userMapper;

public List<User> getUsers(String name, Integer age) {
    LambdaQueryWrapper<User> lambdaQuery = new LambdaQueryWrapper<>();
    
    if (name != null && !name.isEmpty()) {
        lambdaQuery.eq(User::getName, name);
    }
    
    if (age != null) {
        lambdaQuery.eq(User::getAge, age);
    }
    
    return userMapper.selectList(lambdaQuery);
}
```

这种方式不仅简洁，还能在编译时检查字段名是否正确，提高代码的安全性和可维护性。

#### 3. 使用 `Condition`

`Condition` 是一个更高级的封装，可以在复杂查询中使用。它允许你通过静态方法快速创建查询条件。

**示例：**

```java
@Autowired
private UserMapper userMapper;

public List<User> getUsers(String name, Integer age) {
    Wrapper<User> wrapper = Condition.create();
    
    if (name != null && !name.isEmpty()) {
        wrapper.eq("name", name);
    }
    
    if (age != null) {
        wrapper.eq("age", age);
    }
    
    return userMapper.selectList(wrapper);
}
```

这种方式适合需要灵活构建复杂查询条件的场景。

#### 4. 自定义 SQL 查询

对于一些复杂或特定需求的查询，可以直接在 Mapper 接口中编写自定义 SQL 查询，并使用注解或 XML 配置文件进行配置。

**示例（注解）：**

```java
public interface UserMapper extends BaseMapper<User> {

    @Select("SELECT * FROM users WHERE name = #{name} AND age = #{age}")
    List<User> selectByCustomCondition(@Param("name") String name, @Param("age") Integer age);

}
```

**示例（XML）：**

```xml
<mapper namespace="com.example.mapper.UserMapper">
  
  <select id="selectByCustomCondition" parameterType="map" resultType="User">
      SELECT * FROM users WHERE name = #{name} AND age = #{age}
  </select>
  
</mapper>
```

这种方式提供了最大的灵活性，使得开发者可以完全控制 SQL 执行逻辑，确保与原有系统或特殊需求兼容。

### 为什么存在多种实现方式？

1. **灵活性**：不同项目和业务场景对查询有不同的要求。有些场景需要简单快捷的方法，而有些则需要高度定制化和灵活性。
2. **类型安全**：使用 `LambdaQueryWrapper` 可以提供编译时检查，避免手动拼接字符串带来的潜在错误。
3. **简化开发**：对于常见的 CRUD 操作，MyBatis-Plus 提供了便捷的方法来减少重复代码，提高开发效率。
4. **兼容性**：保留自定义 SQL 查询方式，使得开发者可以在需要时完全控制 SQL 执行逻辑，确保与原有系统或特殊需求兼容。
5. **可读性和维护性**：不同的实现方式可以根据团队习惯和代码风格选择最适合的一种，从而提高代码可读性和维护性。

通过提供多种实现方式，MyBatis-Plus 能够更好地适应各种应用场景，为开发者提供更多选择，以便他们根据具体需求选择最合适的方法。这不仅提升了开发效率，也增强了代码质量和可维护性。如果你还没有尝试过这些方法，不妨在你的项目中试一试，相信会给你带来不一样的体验。

By AI