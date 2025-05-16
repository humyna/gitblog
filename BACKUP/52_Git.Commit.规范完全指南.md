# [Git Commit 规范完全指南](https://github.com/humyna/gitblog/issues/52)

> 本文由AI整理生成


## 目录
1. 为什么需要Commit规范
2. Commit Message结构
3. 规范详解
4. 实战示例
5. 工具支持
6. 最佳实践

## 1. 为什么需要Commit规范

在团队协作中,规范的Commit信息可以帮助我们:
- 提供更多的历史信息
- 快速浏览更新记录
- 协助自动化生成CHANGELOG
- 让项目的版本迭代更加清晰

## 2. Commit Message结构

一个标准的Commit Message包含三个部分:
```
<type>(<scope>): <subject>

<body>

<footer>
```

这种结构可以提供完整的提交信息,方便后续的代码审查和版本追踪。

## 3. 规范详解

### 3.1 Type类型

type用于说明commit的类别,常见的类型包括:

```
feat:     新功能(feature)
fix:      修复Bug
docs:     文档变更
style:    代码格式(不影响代码运行的变动)
refactor: 重构(既不是新增功能，也不是修改bug的代码变动)
perf:     性能优化(performance)
test:     增加测试
build:    构建过程或辅助工具的变动
ci:       持续集成的配置文件和脚本变动
chore:    其他改动
```

### 3.2 Scope范围

scope用于说明commit影响的范围,常见的范围包括:

```
core:     核心模块
api:      接口
auth:     认证
user:     用户模块
order:    订单模块
payment:  支付模块
common:   公共模块
utils:    工具类
config:   配置
deps:     依赖
```

### 3.3 Subject主题

subject是commit的简短描述,规范要求:
- 以动词开头,使用第一人称现在时
- 第一个字母小写
- 不要以句号结尾

### 3.4 Body正文

body是对本次commit的详细描述,应当说明:
- 代码变动的动机
- 具体的改动内容
- 可能的影响

### 3.5 Footer页脚

footer主要用于:
- 关闭Issue
- 说明Breaking Changes
- 标记废弃特性

## 4. 实战示例

### 4.1 功能开发示例
```
feat(user): add user management system

- Implement user CRUD operations
- Add role-based access control
- Create user profile management
- Add user search functionality

This feature provides a complete user management solution with:
- User creation and modification
- Role assignment
- Profile management
- Advanced search capabilities

Closes #456
```

### 4.2 Bug修复示例
```
fix(auth): resolve token validation issue

- Fix JWT signature verification
- Add token expiration check
- Implement token refresh mechanism
- Add error handling for invalid tokens

This fix addresses the security vulnerability in token validation.

Closes #789
```

### 4.3 重构示例
```
refactor(core): optimize database queries

- Replace raw SQL with ORM
- Add connection pooling
- Implement query caching
- Optimize join operations

Performance improvements:
- Query execution time reduced by 50%
- Connection management improved
- Better resource utilization

Closes #101
```

## 5. 工具支持

### 5.1 Commitizen

Commitizen是一个撰写合格Commit message的工具:

```bash
# 安装
npm install -g commitizen
npm install -g cz-conventional-changelog

# 初始化
commitizen init cz-conventional-changelog --save-dev --save-exact

# 使用
git cz
```

### 5.2 Commit Lint

CommitLint用于检查提交信息是否符合规范:

```bash
# 安装
npm install @commitlint/cli @commitlint/config-conventional --save-dev

# 配置commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional']
};
```

### 5.3 Husky

Husky可以在Git hooks中执行commitlint检查:

```bash
# 安装
npm install husky --save-dev

# 配置
{
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
}
```

## 6. 最佳实践

### 6.1 应该做的(Do's)

1. 使用规范的类型前缀
```
feat: 添加用户登录功能
fix: 修复登录验证问题
```

2. 写清晰的描述信息
```
feat(auth): implement OAuth2 authentication flow

- Add OAuth2 client configuration
- Implement authorization code flow
- Add user info endpoint
```

3. 包含相关的issue编号
```
fix(login): correct password validation

Closes #123
```

4. 保持提交粒度合适
```
style: format user controller code
fix: resolve null pointer in user service
```

5. 使用现在时态
```
feat: add password reset feature
fix: correct email validation
```

### 6.2 不应该做的(Don'ts)

1. 不写类型前缀
```
× update code
√ feat: add user registration
```

2. 描述信息过于简单
```
× fix: bug fix
√ fix(auth): correct JWT token validation
```

3. 一次提交包含多个不相关的修改
```
× feat: add login and update database
√ feat(auth): implement user login
```

4. 使用过去时态
```
× feat: added login function
√ feat: add login function
```

5. 省略重要信息
```
× fix: update code
√ fix(payment): resolve transaction timeout issue
```

## 7. 总结

规范的Git Commit信息有助于:
1. 提高代码审查效率
2. 自动生成更新日志
3. 方便版本控制
4. 提高项目可维护性
5. 便于团队协作

建议团队:
- 制定统一的提交规范
- 使用工具辅助规范执行
- 定期进行规范审查
- 持续改进提交质量

通过遵循这些规范和最佳实践,我们可以:
- 提高代码质量
- 改善团队协作
- 优化项目管理
- 提升开发效率

记住,好的提交信息是项目历史的重要组成部分,值得我们投入时间去维护。