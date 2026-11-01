# SQL Injection（PortSwigger）

> 官方 Lab 列表以 [官网](https://portswigger.net/web-security/sql-injection) 为准，完成一个打一个勾。
> 速查：[五种数据库差异](db-differences)（注释符 / 拼接 / 查版本 / 查元数据）

## Apprentice

- [√ ] WHERE 子句隐藏数据
- [√ ] 登录绕过
- [√ ] UNION 确定列数
- [√ ] UNION 找文本列
- [√ ] UNION 跨表取数据
- [√ ] UNION 单列取多值

## Practitioner

- [√ ] 查询数据库类型与版本（Oracle）
- [√ ] 查询数据库类型与版本（MySQL / Microsoft）
- [√ ] 列出数据库内容（非 Oracle）
- [√ ] 列出数据库内容（Oracle）
- [ ] 布尔盲注：条件响应
- [ ] 布尔盲注：条件报错
- [ ] 时间盲注
- [ ] 时间盲注 + 信息提取
- [ ] 带外（OOB）交互
- [ ] 过滤绕过（XML 编码）

## Expert

- [ ] 带外（OOB）数据外带

## 笔记

| # | Lab | 难度 | 核心思路 | 链接 |
|---|---|---|---|---|
| 01 | WHERE 子句绕过显示未发布商品 | Apprentice | `' OR 1=1--` 恒真 + 注释符，双条件失效 | [→](hidden-data-where-clause) |
| 02 | 登录绕过（login bypass） | Apprentice | `administrator'--` 注释掉 password 判断 | [→](login-bypass) |
| 03 | UNION attack, retrieving data from other tables | Apprentice | `UNION SELECT username,password` 两列都是文本，直接分列取 | [→](union-retrieving-data) |
| 04 | UNION attack, retrieving multiple values in a single column | Practitioner | 只有一列是文本 → `\|\|` 拼接后塞进单列 | [→](union-multiple-values) |
