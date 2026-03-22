---
name: cloud-iam-audit
description: "分析云IAM权限配置+提权路径+跨账号风险"
metadata:
  tags: "cloud,iam,audit,privilege,escalation"
  difficulty: "hard"
  icon: "🔒"
  category: "云环境"
---

请对 {{cloud_type}} 云环境进行 IAM 安全审计：
1. 使用 query_credentials 查看已获取的云凭据
2. 分析凭据权限范围和潜在提权路径
3. 使用 memory_save 记录关键发现
已知信息: {{context}}
输出：
- 当前权限评估（读/写/管理）
- IAM 提权路径（iam:PassRole/sts:AssumeRole/lambda提权等）
- 跨账号/跨区域访问可能性
- 敏感数据访问范围（S3/数据库/密钥管理）
- 防御绕过建议（CloudTrail规避/GuardDuty规避）
