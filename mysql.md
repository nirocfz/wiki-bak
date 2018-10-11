<!-- TITLE: MySQL -->
<!-- SUBTITLE: MySQL 常用又容易忘的基础命令 -->

创建新库例子

```
CREATE DATABASE `demo` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

创建用户管理这个数据库

```
GRANT ALL PRIVILEGES ON `demo`.* TO `demo_user`@`localhost` IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

给某个表添加单列

```
ALTER TABLE tbl_name ADD [COLUMN] col_name column_definition [FIRST|AFTER col_name];
```

例子：

```
ALTER TABLE user1 ADD age TINYINT UNSIGNED NOT NULL DEFAULT 10;
ALTER TABLE user1 ADD password VARCHAR(32) NOT NULL AFTER username;
ALTER TABLE user1 ADD truename VARCHAR(20) NOT NULL FIRST;
```

## 配置默认 utf8mb4

```
[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
```
