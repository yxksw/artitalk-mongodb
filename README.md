# Artitalk MongoDB 版本

## 项目简介

Artitalk MongoDB 版本是一个基于 Node.js + Express + MongoDB 构建的轻量级说说系统，替代了原来的 LeanCloud 版本。该项目提供了完整的后端 API 服务和前端调用示例，支持用户认证、说说发布、评论管理等功能。

### 主要特性

- **后端 API 服务**：基于 Node.js + Express + MongoDB 构建
- **用户认证**：使用 JWT 进行身份验证
- **管理员模式**：只有管理员可以发布说说
- **完整的 CRUD 操作**：支持说说和评论的增删改查
- **CORS 支持**：跨域请求配置
- **易于部署**：支持 Vercel 等平台部署

## 技术栈

- **后端**：Node.js, Express, MongoDB, Mongoose, JWT
- **前端**：原生 JavaScript (API 客户端)
- **部署**：Vercel (推荐) 或其他 Node.js 服务器

## 目录结构

```
artitalk-mongodb/
├── server/              # 后端服务
│   ├── config/          # 配置文件
│   │   └── db.js        # MongoDB 连接配置
│   ├── middleware/      # 中间件
│   │   ├── admin.js     # 管理员权限验证
│   │   └── auth.js      # 认证中间件
│   ├── models/          # 数据模型
│   │   ├── Comment.js   # 评论模型
│   │   ├── Shuoshuo.js  # 说说模型
│   │   └── User.js      # 用户模型
│   ├── routes/          # 路由
│   │   ├── auth.js      # 认证路由
│   │   ├── comments.js  # 评论路由
│   │   └── shuoshuo.js  # 说说路由
│   └── index.js         # 服务器入口
├── src/                 # 前端资源
│   └── api-client.js    # 前端 API 客户端
├── test.html            # 测试页面
├── FRONTEND_API_GUIDE.md # 前端 API 使用指南
├── VERCEL_DEPLOY.md     # Vercel 部署指南
└── README.md            # 项目说明
```

## 安装和运行

### 1. 环境要求

- Node.js 14.0 或更高版本
- MongoDB 4.0 或更高版本（推荐使用 MongoDB Atlas）

### 2. 安装依赖

```bash
# 进入项目目录
cd artitalk-mongodb

# 安装后端依赖
cd server
npm install
```

### 3. 配置环境变量

在 `server` 目录创建 `.env` 文件：

```env
# MongoDB 连接字符串
MONGODB_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/artitalk?retryWrites=true&w=majority

# JWT 密钥
JWT_SECRET=your_jwt_secret_key

# 端口号
PORT=3000
```

### 4. 运行开发服务器

```bash
# 启动后端服务
cd server
npm start
```

服务将在 `http://localhost:3000` 运行。

## API 端点

### 认证相关

- `POST /api/auth/register` - 注册新用户
- `POST /api/auth/login` - 用户登录
- `GET /api/auth/me` - 获取当前用户信息

### 说说相关

- `GET /api/shuoshuo` - 获取说说列表
- `POST /api/shuoshuo` - 发布新说说（需要管理员权限）
- `PUT /api/shuoshuo/:id` - 更新说说（需要管理员权限）
- `DELETE /api/shuoshuo/:id` - 删除说说（需要管理员权限）

### 评论相关

- `GET /api/comments` - 获取评论列表
- `POST /api/comments` - 发布新评论
- `PUT /api/comments/:id` - 更新评论
- `DELETE /api/comments/:id` - 删除评论

## 前端使用

### 1. 引入 API 客户端

```javascript
// 在 HTML 文件中引入
<script src="path/to/api-client.js"></script>
```

### 2. 初始化 API 客户端

```javascript
// 初始化 API 客户端
const api = new ArtitalkAPI('https://your-api-domain.com/api');
```

### 3. 登录（管理员）

```javascript
// 登录
api.login('admin@example.com', 'password')
  .then(response => {
    console.log('登录成功:', response);
  })
  .catch(error => {
    console.error('登录失败:', error);
  });
```

### 4. 发布说说（需要管理员权限）

```javascript
// 发布说说
api.createShuoshuo('这是一条新说说')
  .then(response => {
    console.log('发布成功:', response);
  })
  .catch(error => {
    console.error('发布失败:', error);
  });
```

### 5. 获取说说列表

```javascript
// 获取说说列表
api.getShuoshuoList()
  .then(shuoshuoList => {
    console.log('说说列表:', shuoshuoList);
  })
  .catch(error => {
    console.error('获取失败:', error);
  });
```

详细的前端 API 使用说明请参考 [FRONTEND_API_GUIDE.md](FRONTEND_API_GUIDE.md)。

## 部署

### Vercel 部署

1. 登录 Vercel 账号
2. 导入项目仓库
3. 配置环境变量（在 Vercel 控制台中）：
   - `MONGODB_URI` - MongoDB 连接字符串
   - `JWT_SECRET` - JWT 密钥
4. 部署项目

详细的 Vercel 部署步骤请参考 [VERCEL_DEPLOY.md](VERCEL_DEPLOY.md)。

### 其他平台部署

可以部署到任何支持 Node.js 的服务器，如：
- Heroku
- AWS EC2
- 阿里云 ECS
- 腾讯云 CVM

## 测试

使用项目根目录下的 `test.html` 文件进行测试：

1. 启动后端服务
2. 在浏览器中打开 `test.html`
3. 测试登录、发布说说、查看列表等功能

## 管理员设置

详细的管理员模式设置指南请参考 [ADMIN_SETUP.md](ADMIN_SETUP.md) 文件。

### 简要步骤

1. 注册用户时，默认角色为 `user`
2. 需要在 MongoDB 中将用户角色修改为 `admin` 才能发布说说
3. 可以通过直接在 MongoDB 中创建管理员用户或修改现有用户角色两种方式设置管理员

## 安全注意事项

- 不要在代码中硬编码 JWT 密钥
- 不要在前端存储敏感信息
- 定期更新 JWT 密钥
- 使用 HTTPS 协议保护 API 通信

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request 来改进这个项目。

## 联系方式

如果有任何问题或建议，请通过以下方式联系：

- GitHub Issues
- Email: your-email@example.com

---

**注意**：本项目是 Artitalk 的 MongoDB 版本，替代了原来的 LeanCloud 版本。前端调用方式有所不同，请参考前端 API 使用指南进行集成。