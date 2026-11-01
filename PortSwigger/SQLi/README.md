# SQL Injection（PortSwigger）

> 官方 Lab 列表以 [官网](https://portswigger.net/web-security/sql-injection) 为准，完成一个打一个勾。

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
| 01 | UNION attack, retrieving data from other tables | Apprentice | UNION 探列 → 定位回显列 → `\|\|` 拼接单列回显 | [→](union-retrieving-data) |
