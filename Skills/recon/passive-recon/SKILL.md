---
name: passive-recon
description: "纯OSINT被动侦察，不触碰目标，使用FOFA/Quake/Hunter三引擎"
metadata:
  tags: "osint,passive,fofa,quake,hunter"
  difficulty: "easy"
  icon: "🕵️"
  category: "侦察"
---

请对目标 {{target}} 执行纯被动信息收集（不触碰目标）：
1. 使用 osint_fofa 搜索目标相关资产
2. 使用 osint_quake 搜索目标相关资产
3. 使用 osint_hunter 搜索目标相关资产
汇总三个引擎的发现，去重后按照以下维度分析：
- IP/域名资产清单
- 开放服务和端口分布
- 使用的技术栈和框架
- 暴露的管理后台和敏感路径
- 证书信息和关联域名
注意：本次仅做被动收集，不直接访问目标。
