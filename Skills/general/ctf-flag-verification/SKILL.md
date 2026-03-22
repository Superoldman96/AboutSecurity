---
name: ctf-flag-verification
description: "CTF/靶场 Flag 提取后的强制验证流程。当任务目标是获取 FLAG{} 时自动触发。防止 LLM 因字符解析错误提交错误 flag。必须用程序化方式验证 flag 长度和完整性"
metadata:
  tags: "ctf,flag,验证,benchmark,capture the flag,靶场,flag验证"
  difficulty: "easy"
  icon: "🏁"
  category: "通用方法论"
---

# CTF Flag 验证方法论

## 核心原则

**LLM 无法可靠地数hex字符或拼接长字符串。** 所有 flag 的提取、拼接、验证必须用程序（Python）完成，绝不手动拼接。

## 强制验证流程

### Step 1: 提取 flag 时使用程序化方法

**当通过 SQL 注入 (EXTRACTVALUE/UPDATEXML) 分段提取数据时：**
- ❌ 不要手动复制各段然后在思考中拼接
- ✅ 写一个 Python 脚本自动发请求、自动分段、自动拼接（参考 sql-injection-methodology 中的 Python 模板）

**当通过文件读取、命令执行等方式获取 flag 时：**
- ✅ 让工具直接返回完整 flag，不要手动截取

### Step 2: 长度验证（必须！）

**如果你在提取过程中获知了 flag 的预期长度（如 SQL LENGTH() 返回 70），必须验证：**

```python
# 在提取 flag 后立即验证
flag = "FLAG{...extracted...}"
expected_length = 70  # 从 LENGTH() 获得
actual_length = len(flag)
print(f"Expected: {expected_length}, Actual: {actual_length}")
assert actual_length == expected_length, f"MISMATCH! Missing {expected_length - actual_length} chars"
```

### Step 3: 格式验证

```python
import re
flag = "FLAG{...}"
assert re.match(r'^FLAG\{[a-fA-F0-9_-]+\}$', flag), f"Invalid flag format: {flag}"
assert flag.startswith('FLAG{'), "Missing FLAG{ prefix"
assert flag.endswith('}'), "Missing } suffix"
```

### Step 4: 如果验证失败

1. **长度不匹配** → 重新提取。如果用 EXTRACTVALUE，改用 Python 自动脚本
2. **格式不对** → 检查是否截断或多余字符
3. **多次失败** → 切换提取方法：
   - EXTRACTVALUE 失败 → 改用 UNION SELECT（无截断限制）
   - UNION 失败 → 改用布尔盲注 + Python 自动化脚本
   - 手工失败 → 改用 sqlmap `--dump`

## 常见错误模式

| 错误 | 原因 | 解决 |
|------|------|------|
| Flag 少 1-2 个字符 | EXTRACTVALUE 32字符截断 + 手动拼接丢字符 | 用 Python 脚本自动提取 |
| Flag 多 1-2 个字符 | SUBSTRING 起始位置重叠 | 检查 SUBSTRING 参数 |
| Flag 中间有错误字符 | LLM 误读 hex 字符 | 用 Python re.search 提取，不要手动复制 |
| Flag 格式不对 | 提取了错误的数据 | 重新确认表名和列名 |
