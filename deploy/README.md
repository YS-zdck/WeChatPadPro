# WeChatPadPro Docker 部署教程

## 目录
- [环境要求](#环境要求)
- [快速部署](#快速部署)
- [配置说明](#配置说明)
- [常见问题](#常见问题)
- [维护指南](#维护指南)

## 环境要求

### 基础环境
- Docker 19.03.0+
- Docker Compose 1.27.0+
- 操作系统：Linux/Windows/MacOS
- 最小配置：2核CPU/4G内存/50G硬盘

### 端口要求
- 1239: WeChatPadPro 服务端口
- 6379: Redis 服务端口（可选）
- 3306: MySQL 服务端口（可选）

## 快速部署

### 方式一：直接从 GitHub 拉取部署

```bash
# 1. 创建并进入部署目录
mkdir -p wechatpadpro && cd wechatpadpro

# 2. 下载 docker-compose.yml
curl -O https://raw.githubusercontent.com/luolin-ai/WeChatPadPro/main/deploy/docker-compose.yml

# 3. 启动服务
docker-compose up -d

# 4. 查看服务状态
docker-compose ps
```

### 方式二：在项目目录中部署

```bash
# 1. 克隆项目
git clone https://github.com/luolin-ai/WeChatPadPro.git

# 2. 进入部署目录
cd WeChatPadPro/deploy

# 3. 启动服务
docker-compose up -d

# 4. 查看服务状态
docker-compose ps
```

## 配置说明

### docker-compose.yml 配置详解

```yaml
version: '3.8'
services:
  wechatpadpro:
    # WeChatPadPro 主服务
    image: luolinaiwis/wechatpadpro:v0.1
    ports:
      - "1239:1239"    # 服务端口，可根据需要修改
    environment:
      - DEBUG=false    # 调试模式开关
      - ADMIN_KEY=12345    # 管理员密钥
      - REDIS_PASS=123456    # Redis密码
      # 其他环境变量...

  redis:
    # Redis 服务
    image: redis:7-alpine
    command: redis-server --requirepass 123456    # Redis密码

  mysql:
    # MySQL 服务
    image: luolinaiwis/wechatpadpro-mysql:v0.1
    environment:
      - MYSQL_ROOT_PASSWORD=123456    # Root密码
      - MYSQL_USER=wechatpadpro    # 应用用户
      - MYSQL_PASSWORD=123456    # 应用用户密码
```

### 环境变量说明

| 变量名 | 说明 | 默认值 | 是否必填 |
|--------|------|--------|----------|
| DEBUG | 调试模式 | false | 否 |
| ADMIN_KEY | 管理员密钥 | 12345 | 是 |
| PORT | 服务端口 | 1239 | 否 |
| REDIS_PASS | Redis密码 | 123456 | 是 |
| MYSQL_CONNECT_STR | MySQL连接串 | 见配置文件 | 是 |

## 常见问题

### 1. 服务无法启动

检查步骤：
```bash
# 查看服务日志
docker-compose logs

# 查看具体服务日志
docker-compose logs wechatpadpro
docker-compose logs mysql
docker-compose logs redis
```

### 2. 数据持久化

数据默认保存在以下 Docker volumes 中：
- wechatpadpro_data：应用数据
- wechatpadpro_logs：应用日志
- redis_data：Redis数据
- mysql_data：MySQL数据

查看数据卷：
```bash
docker volume ls | grep wechatpadpro
```

### 3. 服务重启

```bash
# 重启所有服务
docker-compose restart

# 重启单个服务
docker-compose restart wechatpadpro
```

## 维护指南

### 日常维护

1. 查看服务状态：
```bash
docker-compose ps
```

2. 查看服务日志：
```bash
docker-compose logs -f --tail=100
```

3. 备份数据：
```bash
# 备份 MySQL 数据
docker exec wechatpadpro_mysql mysqldump -u root -p123456 wechatpadpro > backup.sql
```

### 版本升级

1. 拉取最新镜像：
```bash
docker-compose pull
```

2. 重新部署服务：
```bash
docker-compose up -d
```

### 服务扩展

如需扩展服务配置，可以创建 `docker-compose.override.yml` 文件：

```yaml
version: '3.8'
services:
  wechatpadpro:
    environment:
      - CUSTOM_VAR=value
    volumes:
      - ./custom_config:/app/config
```

## 安全建议

1. 修改默认密码
   - 修改 ADMIN_KEY
   - 修改 MySQL 密码
   - 修改 Redis 密码

2. 限制端口访问
   - 只对必要端口开放外部访问
   - 使用防火墙限制 IP 访问

3. 定期备份
   - 备份 MySQL 数据
   - 备份应用数据

## 技术支持

- GitHub Issues: https://github.com/luolin-ai/WeChatPadPro/issues
- Telegram 群组: https://t.me/+LK0JuqLxjmk0ZjRh
