# <span style="color:#1e3a8a;">HTTP 请求与数据获取</span>

## <span style="color:#2563eb;">请求数据格式</span>

HTTP 请求由三部分组成：

| 组成部分    | 位置   | 说明                        |
| ------- | ---- | ------------------------- |
| **请求行** | 第一行  | 包含请求方式、资源路径、协议版本          |
| **请求头** | 第二行起 | 键值对格式（`key: value`），传递元信息 |
| **请求体** | 空行之后 | 存放请求参数（主要用于 POST 请求）      |

## <span style="color:#2563eb;">常见请求头</span>

| 请求头               | 含义                     |
| ----------------- | ---------------------- |
| `Host`            | 请求的目标主机名               |
| `User-Agent`      | 客户端标识（含浏览器、操作系统等信息）    |
| `Accept`          | 客户端可接受的响应数据类型（MIME 类型） |
| `Accept-Language` | 客户端的语言偏好               |
| `Accept-Encoding` | 客户端支持的压缩编码类型           |
| `Content-Type`    | 请求体的数据类型               |
| `Content-Length`  | 请求体的大小（单位：字节）          |

## <span style="color:#2563eb;">请求方式对比</span>

| 对比项  | GET            | POST                                                         |
| ---- | -------------- | ------------------------------------------------------------ |
| 参数位置 | 请求行（URL 查询字符串） | 请求体中                                                         |
| 请求体  | 无              | 有                                                            |
| 参数大小 | 受 URL 长度限制     | <span style="color:#d97706;">不受 URL 长度限制，但服务器可配置请求体上限</span> |

## <span style="color:#2563eb;">请求数据获取</span>

### <span style="color:#3b82f6;">@RequestMapping 路径映射</span>

`@RequestMapping("/request")`：规定请求路径，当请求路径匹配时调用该方法。

### <span style="color:#3b82f6;">HttpServletRequest 常用方法</span>

| 方法                        | 作用            | 返回示例               |
| ------------------------- | ------------- | ------------------ |
| `getMethod()`             | 获取请求方式        | `"GET"` / `"POST"` |
| `getRequestURI()`         | 获取请求路径        | `"/user"`          |
| `getParameter("name")`    | 获取请求参数        | `"zhangsan"`       |
| `getHeader("User-Agent")` | 获取指定请求头       | `"Mozilla/5.0..."` |
| `getCookies()`            | 获取所有 Cookie   | Cookie 数组          |
| `getSession()`            | 获取 Session 对象 | HttpSession 对象     |
| `getRemoteAddr()`         | 获取客户端 IP      | `"127.0.0.1"`      |
| `getInputStream()`        | 获取请求体输入流      | 可读取 JSON 等数据       |
