# SQLite 兼容性修复说明

## 问题描述

**错误信息：**
```
激活失败：SQLSTATE[HY000]: General error: 1 near "UNSIGNED": syntax error。
```

**原因：** 原代码使用了 MySQL 特有的 `UNSIGNED` 关键字，SQLite 不支持此语法。

## 修复内容

修改了 [Plugin.php](Plugin.php) 中的两个表创建方法：
- `createTokenTable()` - 第 348-380 行
- `createFailLogTable()` - 第 391-421 行

### 修复逻辑

```php
// 检测数据库类型
$adapterName = $db->getAdapterName();

if (strpos($adapterName, 'Mysql') !== false) {
    // MySQL: 使用 INT UNSIGNED, ENGINE, CHARSET
} else {
    // SQLite: 使用 INTEGER, 单独创建索引
}
```

### 关键差异

| 特性 | MySQL | SQLite |
|------|-------|--------|
| 整数类型 | `INT(10) UNSIGNED` | `INTEGER` |
| 布尔类型 | `TINYINT(1)` | `INTEGER` |
| 索引 | 表内定义 `INDEX` | 单独 `CREATE INDEX` |
| 存储引擎 | `ENGINE=InnoDB` | 不支持 |
| 字符集 | `CHARSET=utf8mb4` | 不支持 |

## 使用方法

1. 更新代码到最新版本
2. 进入 Typecho 后台 → 控制台 → 插件
3. 启用 Passport 插件

现在插件同时支持 MySQL 和 SQLite 数据库。

## 技术细节

### MySQL 表结构（保持原样）
```sql
CREATE TABLE IF NOT EXISTS `typecho_password_reset_tokens` (
    `token` VARCHAR(64) NOT NULL,
    `uid` INT(10) UNSIGNED NOT NULL,
    `created_at` INT(10) UNSIGNED NOT NULL,
    `used` TINYINT(1) DEFAULT 0,
    PRIMARY KEY (`token`),
    INDEX `uid` (`uid`),
    INDEX `created_at` (`created_at`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
```

### SQLite 表结构（兼容修改）
```sql
-- 创建表
CREATE TABLE IF NOT EXISTS `typecho_password_reset_tokens` (
    `token` VARCHAR(64) NOT NULL,
    `uid` INTEGER NOT NULL,
    `created_at` INTEGER NOT NULL,
    `used` INTEGER DEFAULT 0,
    PRIMARY KEY (`token`)
);

-- 单独创建索引
CREATE INDEX IF NOT EXISTS `idx_uid` ON `typecho_password_reset_tokens` (`uid`);
CREATE INDEX IF NOT EXISTS `idx_created_at` ON `typecho_password_reset_tokens` (`created_at`);
```

## 支持的数据库

- ✅ MySQL 5.5+
- ✅ MariaDB 10.0+
- ✅ SQLite 3.x
- ⚠️ PostgreSQL（理论支持，未测试）

---

**更新日期：** 2025-11-04
**版本：** 0.1.1+
