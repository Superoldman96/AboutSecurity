---
name: osint-gather
description: "使用多个搜索引擎（FOFA/Quake/Hunter）收集目标资产情报，汇总分析"
metadata:
  tags: "osint,fofa,quake,hunter"
  difficulty: "easy"
  icon: "🌐"
  category: "侦察"
---

请使用 OSINT 搜索引擎收集目标 {{target}} 的资产情报：
1. 使用 osint_fofa 查询相关资产
2. 使用 osint_quake 查询相关资产
3. 使用 osint_hunter 查询相关资产
汇总所有发现的资产（IP、域名、端口、服务），去重后给出分析报告。
重点关注：开放的高危端口、已知的漏洞组件、暴露的管理后台。
