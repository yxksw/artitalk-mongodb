# 管理员模式设置指南

## 管理员模式概述

Artitalk MongoDB 版本采用了管理员模式，只有拥有管理员权限的用户才能发布说说。这一设计可以有效防止未授权用户发布内容，提高系统的安全性。

## 管理员设置步骤

### 方法一：直接在 MongoDB 中创建管理员用户

1. **登录 MongoDB Atlas** 或打开本地 MongoDB 管理工具

2. **连接到你的数据库**

3. **选择 artitalk 数据库**

4. **在 users 集合中插入管理员用户**：
   ```javascript
   db.users.insertOne({
     email: "admin@example.com",
     password: "$2a$10$EixZaYVK1fsbw1ZfbX3OXePaWxn96p36WQoeG6Lruj3vjPGga31lW", // 密码: admin123
     role: "admin",
     createdAt: new Date(),
     updatedAt: new Date()
   })
   ```

   > 注意：上面的密码是经过 bcrypt 加密的，对应的明文密码是 `admin123`。建议使用自己的密码并通过 bcrypt 加密。

### 方法二：先注册用户，再修改角色

1. **注册用户**：使用注册接口 `POST /api/auth/register` 注册一个新用户

2. **登录 MongoDB 管理工具**

3. **找到刚注册的用户**：在 users 集合中找到该用户文档

4. **修改用户角色**：将 `role` 字段从 `user` 修改为 `admin`
   ```javascript
   db.users.updateOne(
     { email: "user@example.com" },
     { $set: { role: "admin" } }
   )
   ```

## 管理员权限说明

### 拥有的权限

- **发布说说**：可以通过 `POST /api/shuoshuo` 接口发布新说说
- **更新说说**：可以通过 `PUT /api/shuoshuo/:id` 接口更新说说
- **删除说说**：可以通过 `DELETE /api/shuoshuo/:id` 接口删除说说
- **管理用户**：可以查看和管理其他用户（如果实现了相关接口）

### 普通用户权限

- **查看说说**：可以通过 `GET /api/shuoshuo` 接口查看说说列表
- **发布评论**：可以通过 `POST /api/comments` 接口发布评论
- **更新评论**：可以通过 `PUT /api/comments/:id` 接口更新自己的评论
- **删除评论**：可以通过 `DELETE /api/comments/:id` 接口删除自己的评论

## 管理员登录

1. **使用登录接口**：`POST /api/auth/login`

2. **请求参数**：
   ```json
   {
     "email": "admin@example.com",
     "password": "admin123"
   }
   ```

3. **登录成功后**：会返回 JWT token，用于后续的管理员操作

4. **在前端使用**：登录后，token 会自动存储在 localStorage 中，后续的管理员操作会自动带上该 token

## 前端测试

可以使用项目根目录下的 `test.html` 文件测试管理员功能：

1. **打开 test.html**：在浏览器中打开该文件

2. **登录**：使用管理员账号登录

3. **发布说说**：登录成功后，会显示发布说说的表单

4. **测试其他功能**：可以测试查看说说列表、发布评论等功能

## 安全注意事项

1. **保护管理员账号**：不要将管理员账号密码泄露给他人

2. **使用强密码**：管理员账号应使用强密码，包含字母、数字和特殊字符

3. **定期更新密码**：定期更换管理员密码，提高安全性

4. **限制管理员数量**：只创建必要的管理员账号，避免过多的管理员

5. **使用 HTTPS**：在生产环境中使用 HTTPS 协议，保护 API 通信安全

## 常见问题

### Q: 为什么我无法发布说说？
A: 请检查你是否以管理员身份登录，以及你的账号是否具有 `admin` 角色。

### Q: 如何查看当前用户的角色？
A: 可以通过 `GET /api/auth/me` 接口查看当前用户信息，包括角色。

### Q: 忘记管理员密码怎么办？
A: 可以在 MongoDB 中直接更新用户密码，或者使用重置密码功能（如果实现了相关接口）。

### Q: 可以有多个管理员吗？
A: 是的，可以在 MongoDB 中创建多个具有 `admin` 角色的用户。

## 相关接口

- **用户注册**：`POST /api/auth/register`
- **用户登录**：`POST /api/auth/login`
- **获取当前用户信息**：`GET /api/auth/me`
- **发布说说**：`POST /api/shuoshuo`（需要管理员权限）
- **更新说说**：`PUT /api/shuoshuo/:id`（需要管理员权限）
- **删除说说**：`DELETE /api/shuoshuo/:id`（需要管理员权限）

## 总结

管理员模式是 Artitalk MongoDB 版本的重要特性，通过合理设置管理员权限，可以有效控制内容发布，提高系统安全性。希望本指南能帮助你顺利设置和使用管理员模式。