---
name: ad-attack
description: "AD域信息收集+Kerberoasting+委派攻击路径分析"
metadata:
  tags: "ad,domain,kerberos,delegation"
  difficulty: "hard"
  icon: "🏰"
  category: "内网渗透"
---

请对域 {{domain}}（域控: {{dc_ip}}）执行渗透分析：
1. 使用 scan_port 扫描域控开放端口（389/445/88/135/3268）
2. 使用 query_credentials 查看已有的域凭据
3. 使用 lateral_list_methods 列出域渗透相关技术
4. 使用 privesc_check_windows 检查域提权向量
5. 使用 lateral_generate_command 生成域攻击命令
分析并输出：
- 域信息收集清单（LDAP查询、SPN枚举、GPP密码）
- Kerberoasting 可行性评估
- 委派攻击路径（非约束委派/约束委派/RBCD）
- 域控攻击路径（ZeroLogon/PrintNightmare/ADCS）
- 黄金票据/白银票据生成建议
