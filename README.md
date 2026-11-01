# Web Security Notes

> 个人 Web 安全靶场 Writeup 与学习笔记，持续更新。

- 方向：Web 安全（SQL 注入 / XSS / 访问控制 / SSRF …）
- 靶场：[PortSwigger Web Security Academy](https://portswigger.net/web-security)
- 每篇结构：一句话结论 → 验证过程 → 原理 → 修复建议 → 踩坑

## 进度

| 模块 | 状态 | 笔记 |
|---|---|---|
| SQL injection | 🚧 进行中 | [PortSwigger/SQLi](PortSwigger/SQLi) |
| XSS | ⬜ 待开始 | — |
| Access control | ⬜ 待开始 | — |
| SSRF | ⬜ 待开始 | — |

## 目录

```
.
├── PortSwigger/SQLi/     # SQL 注入 Lab writeup
├── CTF/                  # 比赛题解（赛后公开）
├── Tools/                # Burp、sqlmap 等工具笔记
└── _template.md          # 新增 writeup 时复制这份模板
```

## 关于

信息安全专业在读，目前主攻 Web 方向，目标是通过手工复现理解每一类漏洞的成因，而不是依赖自动化工具出结果。

> 本仓库仅记录**授权靶场**与**已结束比赛**的题目，不含任何真实目标的测试记录。
