# MCP管理界面技术设计文档

## 1. 概述

本文档旨在详细描述MCP管理界面的技术实现方案，包括前后端架构设计、数据库设计、API接口定义等技术细节。

## 2. 技术架构

### 2.1 整体架构
```
前端(Vue 3.3.4 + Element Plus 2.3.4) 
    ↓↑ (HTTP/JSON)
后端(Spring Boot 2.7.18)
    ↓↑ (JDBC)
数据库(SQLite)
```

### 2.2 技术栈详情
- 前端框架：Vue 3.3.4
- UI组件库：Element Plus 2.3.4
- 前端依赖管理：直接引入CDN资源，不使用npm
- 后端框架：Spring Boot 2.7.18 (spring-boot-starter-web)
- 数据库：SQLite
- 构建工具：Maven (后端)

## 3. 数据库设计

### 3.1 数据库表结构

#### MCP服务表 (mcp_servers)
| 字段名 | 类型 | 约束 | 描述 |
|--------|------|------|------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | 主键 |
| name | TEXT | NOT NULL UNIQUE | 服务名称 |
| type | TEXT | NOT NULL | 服务类型 (sse/http) |
| url | TEXT | NOT NULL | 服务URL |
| headers | TEXT | NULL | 请求头信息(JSON格式) |
| timeout | INTEGER | DEFAULT 30 | 超时时间(秒) |
| created_at | DATETIME | DEFAULT CURRENT_TIMESTAMP | 创建时间 |
| updated_at | DATETIME | DEFAULT CURRENT_TIMESTAMP | 更新时间 |

## 4. 后端API设计

### 4.1 API端点定义

#### 获取所有MCP服务
- **URL**: `GET /api/mcp/servers`
- **描述**: 获取所有已配置的MCP服务列表
- **响应**:
```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "user info system",
      "type": "sse",
      "url": "http://127.0.0.1:10002/sse/",
      "headers": "{\"Authorization\": \"Bearer 54bfe0d6-803f-4f60-9bed-0d7709731011\"}",
      "timeout": 60,
      "created_at": "2023-01-01T00:00:00",
      "updated_at": "2023-01-01T00:00:00"
    }
  ]
}
```

#### 添加MCP服务
- **URL**: `POST /api/mcp/servers`
- **描述**: 添加新的MCP服务
- **请求体**:
```json
{
  "name": "service name",
  "type": "sse",
  "url": "http://example.com/sse/",
  "headers": "{\"Authorization\": \"Bearer token\"}",
  "timeout": 60
}
```
- **响应**:
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": 2,
    "name": "service name",
    "type": "sse",
    "url": "http://example.com/sse/",
    "headers": "{\"Authorization\": \"Bearer token\"}",
    "timeout": 60,
    "created_at": "2023-01-01T00:00:00",
    "updated_at": "2023-01-01T00:00:00"
  }
}
```

#### 删除MCP服务
- **URL**: `DELETE /api/mcp/servers/{id}`
- **描述**: 根据ID删除指定的MCP服务
- **响应**:
```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

#### 测试MCP服务
- **URL**: `POST /api/mcp/servers/test`
- **描述**: 测试MCP服务是否可用
- **请求体**:
```json
{
  "type": "sse",
  "url": "http://example.com/sse/",
  "headers": "{\"Authorization\": \"Bearer token\"}",
  "timeout": 60
}
```
- **响应**:
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "available": true,
    "toolCount": 5
  }
}
```

#### 获取MCP服务工具列表
- **URL**: `GET /api/mcp/servers/{id}/tools`
- **描述**: 获取指定MCP服务支持的工具列表
- **响应**:
```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "name": "get_user_info",
      "description": "获取用户信息",
      "inputSchema": {
        "type": "object",
        "properties": {
          "userId": {
            "type": "string",
            "description": "用户ID"
          }
        },
        "required": ["userId"]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string"
          },
          "email": {
            "type": "string"
          }
        }
      }
    }
  ]
}
```

## 5. 前端设计

### 5.1 项目结构
```
src/
├── assets/                 # 静态资源
├── components/             # 组件
│   ├── McpServerCard.vue   # MCP服务卡片组件
│   ├── McpServerForm.vue   # MCP服务表单组件
│   └── McpToolList.vue     # MCP工具列表组件
├── views/                  # 页面视图
│   └── McpManagement.vue   # MCP管理主页面
├── utils/                  # 工具函数
│   └── api.js             # API请求封装
└── App.vue                # 根组件
```

### 5.2 核心组件设计

#### McpManagement.vue (主页面)
- 功能：展示所有MCP服务卡片，提供新增功能
- 包含：McpServerCard组件列表、新增按钮

#### McpServerCard.vue (服务卡片)
- 功能：展示单个MCP服务信息，提供查看工具和删除操作
- 属性：MCP服务对象
- 事件：查看工具、删除

#### McpServerForm.vue (服务表单)
- 功能：提供添加MCP服务的表单界面
- 包含：表单输入方式切换、表单字段、JSON输入框、测试按钮、保存按钮

#### McpToolList.vue (工具列表)
- 功能：展示指定MCP服务的工具列表
- 属性：工具列表数据

### 5.3 前端依赖
通过CDN直接引入：
- Vue 3.3.4
- Element Plus 2.3.4
- Axios (用于HTTP请求)

## 6. 安全设计

### 6.1 敏感信息处理
- 前端显示时对headers中的敏感信息(如Authorization)进行掩码处理
- 后端存储时对敏感信息进行加密存储(可选)

### 6.2 输入验证
- 前端对用户输入进行基础验证
- 后端对所有输入参数进行严格验证
- 防止SQL注入和XSS攻击

## 7. 错误处理

### 7.1 前端错误处理
- 网络请求错误提示
- 表单验证错误提示
- 操作结果反馈提示

### 7.2 后端错误处理
- 统一异常处理机制
- 详细的错误码和错误信息
- 日志记录

## 8. 部署方案

### 8.1 前端部署
- 静态文件部署到Web服务器
- 通过CDN引入依赖库

### 8.2 后端部署
- 打包为可执行JAR文件
- 内嵌SQLite数据库
- 可独立运行

## 9. 后续优化建议

1. 实现MCP服务编辑功能
2. 添加MCP服务状态监控机制
3. 实现批量操作功能
4. 增加服务分组管理功能
5. 添加操作日志记录
