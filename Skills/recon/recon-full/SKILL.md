---
name: recon-full
description: "对目标域名进行完整的资产侦察：子域名枚举、端口扫描、URL存活检测、指纹识别、POC扫描"
metadata:
  tags: "recon,subdomain,port,fingerprint,poc"
  difficulty: "medium"
  icon: "🔍"
  category: "侦察"
---

请对目标 {{target}} 执行完整的资产侦察流程：
1. 先使用 scan_dns 进行子域名枚举
2. 对发现的子域名使用 scan_port 进行端口扫描
3. 使用 scan_urlive 检测 URL 存活
4. 使用 scan_finger 进行指纹识别
5. 最后使用 poc_scan_web 进行漏洞扫描
每步完成后总结发现，最终给出完整的资产侦察报告。
