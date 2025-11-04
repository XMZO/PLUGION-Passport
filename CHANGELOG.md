# 更新日志

所有重要的项目变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

---

## [0.1.2-security] - 2025-11-04

### 🔐 安全修复（Security Fixes）

#### 高危漏洞修复
- **[P0-1]** 修复令牌生成使用弱随机数的问题（CVSS 9.1）
  - 改用密码学安全的 `random_bytes()` 生成令牌
  - Fallback 到 `openssl_random_pseudo_bytes()`
  - 移除不安全的 `mt_rand()` 实现
  - 影响文件：`Widget.php:216-229`

- **[P0-2]** 修复 Geetest 使用 HTTP 协议的问题（CVSS 7.4）
  - 将 Geetest 验证请求改为 HTTPS
  - 防止中间人攻击（MITM）
  - 影响文件：`Widget.php:476-477`

- **[P0-3]** 修复邮件模板存储型 XSS 漏洞（CVSS 6.5）
  - 对所有插入邮件模板的变量进行 HTML 转义
  - 使用 `htmlspecialchars()` 防止脚本注入
  - 影响文件：`Widget.php:508-518`

#### 中危漏洞修复
- **[P1-4]** 改进 HMAC 密钥生成 fallback 逻辑（CVSS 7.5）
  - 优先使用 `random_bytes()`
  - Fallback 到 `openssl_random_pseudo_bytes()`
  - 移除不安全的 `mt_rand()` fallback
  - 不支持时返回空字符串，提示用户手动设置
  - 影响文件：`Plugin.php:318-349`

- **[P1-5]** 添加后台解封表单 CSRF 保护（CVSS 5.4）
  - 在 `handleUnblockIp()` 中添加 CSRF 验证
  - 在解封表单中添加 CSRF token
  - 防止跨站请求伪造攻击
  - 影响文件：`Plugin.php:193-211, 246-252`

### 📝 文档更新
- 添加 `SECURITY-FIX-v0.1.2.md` 安全修复报告
- 添加 `CHANGELOG.md` 更新日志
- 更新 `SQLite兼容性修复.md`

### ⚠️ 破坏性变更
- PHP < 7.0 且无 openssl 扩展的服务器无法自动生成令牌和 HMAC 密钥
- 需要手动设置 HMAC 密钥或升级 PHP 至 7.0+

### 🔄 向后兼容性
- ✅ 已生成的旧令牌仍有效（1 小时后自动过期）
- ✅ 现有配置无需修改
- ✅ 数据库结构无变化

---

## [0.1.1] - 2025-11-04

### 🐛 修复（Fixed）
- 修复 SQLite 数据库兼容性问题
  - 移除 MySQL 特有的 `UNSIGNED` 关键字
  - 根据数据库类型动态生成 SQL 语句
  - 支持 MySQL 和 SQLite
  - 影响文件：`Plugin.php:348-421`

### 📝 文档（Documentation）
- 添加 `SQLite兼容性修复.md` 说明文档

---

## [0.1.1-pre] - 2025-XX-XX

### ✨ 新增（Added）
- 初始版本发布
- 密码找回功能
- SMTP 邮件发送
- 多验证码支持（reCAPTCHA v2、hCaptcha、Geetest v4）
- IP 速率限制和封禁机制
- HMAC 签名验证
- 后台 IP 管理界面

### 🔧 功能（Features）
- 支持 MySQL 和 MariaDB 数据库
- 1 小时令牌有效期
- 自动清理过期令牌
- 密码复杂度验证
- 防用户枚举攻击
- 邮件模板自定义

---

## 版本说明

### 版本号格式
- **主版本号**：重大架构变更或不兼容的 API 修改
- **次版本号**：向后兼容的功能性新增
- **修订号**：向后兼容的问题修正
- **后缀**：
  - `-security`：安全更新
  - `-pre`：预发布版本
  - `-beta`：测试版本

### 更新建议
- 🔴 **安全更新**（-security）：**强烈建议立即更新**
- 🟡 **功能更新**：建议更新
- 🟢 **修复更新**：可选更新

---

## 链接

- [GitHub 仓库](https://github.com/little-gt/PLUGION-Passport)
- [问题反馈](https://github.com/little-gt/PLUGION-Passport/issues)
- [安全公告](./SECURITY-FIX-v0.1.2.md)
