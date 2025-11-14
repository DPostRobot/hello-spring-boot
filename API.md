### 🚀 API 文档: 用户管理
#### 1. 概述
本文档描述了用于管理用户的 RESTful API。该API支持对用户资源进行**创建(Create)**、**读取(Read)**、**更新(Update)** 和 **删除(Delete)** 操作。

---

#### 2. 基础 URL
所有API端点的基础路径为：/api/v1/users

---

#### 3. 数据模型
##### User (用户)
API中的核心数据对象。

| 字段名 | 类型 | 描述 | 示例  |
|------|-----|-------|--------------|
| `id` | Long | 用户的唯一标识符 (在创建后由服务器自动生成) | `1` |
| `name` | String | 用户的姓名 | `"John Doe"` |
| `age` | Integer | 用户的年龄 | `30` |


##### 示例 JSON (请求 - 创建用户):
```JSON
{
"name": "Jane Doe",
"age": 28
}
```

##### 示例 JSON (响应 - 获取用户):
```JSON
{
"id": 1,
"name": "Jane Doe",
"age": 28
}
```

---

#### 4. API 端点详解
##### 4.1 创建新用户
- HTTP 方法: `POST`
- 端点: `/api/v1/users`
- 描述: 创建一个新用户。
- 请求体 (Request Body):
  - `User` 对象的 JSON 表示（`id`字段应省略）。
  - 示例:
  ```JSON
  {
     "name": "Admin User",
     "age": 42
  }
  ```
- 成功响应 (Success Response):
  - Code: `200 OK`
  - Content: 新创建的 `User` 对象，包含服务器生成的 `id`。
  - 示例:
  ```JSON
  {
      "id": 3,
      "name": "Admin User",
      "age": 42
  }
  ```
- 错误响应 (Error Response):`400 Bad Request`: 如果请求的 JSON 格式不正确。

##### 4.2 获取所有用户
- HTTP 方法: `GET`
- 端点: `/api/v1/users`
- 描述: 检索所有用户的列表。
- 请求体 (Request Body): 无
- 成功响应 (Success Response):
  - Code: `200 OK`
  - Content: `User` 对象的 JSON 数组。如果数据库中没有用户，将返回一个空数组 `[]`。
  - 示例:
  ```JSON
  [
    {
    "id": 1,
    "name": "John Doe",
    "age": 30
    },
    {
    "id": 2,
    "name": "Jane Doe",
    "age": 28
    }
  ]
  ```

##### 4.3 根据ID获取单个用户
- HTTP 方法: `GET`
- 端点: `/api/v1/users/{id}`
- 描述: 检索指定ID的单个用户。
- 路径参数 (Path Parameter):
  - `id` (Long): 要检索的用户的ID。
- 请求体 (Request Body): 无
- 成功响应 (Success Response):
  - Code: `200 OK`
  - Content: 匹配到的 `User` 对象。
  - 示例:
  ```JSON
  {
    "id": 1,
    "name": "John Doe",
    "age": 30
  }
  ``` 
- 错误响应 (Error Response):
  - Code: `404 NOT_FOUND`
  - Content: 当找不到指定ID的用户时返回（详情见 5. 错误处理）。
  - 示例:
  ```
  JSON{
    "message": "User not found with id: 99"
  }
  ```
##### 4.4 根据ID删除用户

- HTTP 方法: DELETE
- 端点: `/api/v1/users/{id}`
- 描述: 删除指定ID的用户。
- 路径参数 (Path Parameter):
  - `id` (Long): 要删除的用户的ID。
- 请求体 (Request Body): 无
- 成功响应 (Success Response):
  - Code: `200 OK`
  - Content: 无内容。
- 错误响应 (Error Response):
  - Code: `404 NOT_FOUND`
  - Content: 当试图删除一个不存在的用户时返回（详情见 5. 错误处理）。
  - 示例:
  ```
  JSON
  {
    "message": "User not found with id: 99"
  }
  ```
  
##### 4.5 更新用户信息 
- HTTP 方法: PUT
- 端点: `/api/v1/users/{id}`
- 描述:
  - 查找具有指定ID的用户，并使用请求体中的数据**完全替换**该用户的信息。
- 路径参数 (Path Parameter):
  - `id` (Long): 要更新的用户的ID。
- 请求体 (Request Body):
  - `User` 对象的 JSON 表示，包含该用户所有字段的更新后信息。`id` 字段在请求体中是可选的，但如果提供，应与URL中的 `id` 匹配。
  - 示例 (更新ID为 1 的用户):
  ```JSON
  {
    "name": "John A. Doe",
    "age": 31
  }
  ```
- 成功响应 (Success Response):
  - Code: `200 OK`
  - Content: 更新后的 `User` 对象。
  - 示例:
  ```JSON
  {
    "id": 1,
    "name": "John A. Doe",
    "age": 31
  }
  ```
- 错误响应 (Error Response):
  - Code: `404 NOT_FOUND`
  - Content: 当试图更新一个不存在的用户时返回。
  - 示例:
  ```JSON
  {
    "message": "User not found with id: 99"
  }
  ```
  - Code: `400 Bad Request`
  - Content: 如果请求的 JSON 格式不正确或缺少必要字段。

---

#### 5. 错误处理
本项目通过 `GlobalExceptionHandler` 类实现全局异常处理。
##### 5.1 ApiError (错误响应模型)
当发生特定错误（如 `UserNotFoundException`）时，API将返回一个标准化的 `ApiError` 对象。
| 字段名 | 类型 | 描述 |
| ---- | ---- | ---- |
| `message` | String | 错误的详细描述信息 |

##### 5.2 常见错误
- Code: `404 NOT_FOUND`
- 触发条件: 尝试通过ID获取、删除或更新一个不存在的用户。
- 响应体:
    ```JSON
    {
      "message": "User not found with id: [ID]"
    }
    ```