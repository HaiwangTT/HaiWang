# 🌊 HaiWang - 海王

[![Go 1.22+](https://img.shields.io/badge/Go-1.22%2B-00ADD8?logo=go&logoColor=white)](https://go.dev/)
[![SQLite](https://img.shields.io/badge/SQLite-modernc.org%2Fsqlite-003B57?logo=sqlite&logoColor=white)](https://pkg.go.dev/modernc.org/sqlite)
[![sing-box](https://img.shields.io/badge/sing--box-vless--reality%20%7C%20hysteria2-0F9D58)](https://sing-box.sagernet.org/)
[![License: MIT](https://img.shields.io/badge/license-MIT-2ea44f)](LICENSE)

`HaiWang - 海王 专为追求极致网络体验的极客设计。它摒弃了传统面板的臃肿，专注于核心的流量转发效率与深度指纹伪装，助你像大海之王一样，在复杂的网络环境中自由穿梭，连接万物。
一个基于 Go 和 sing-box 的轻量代理控制平面，面向“账号管理、节点编排、订阅分发、运行时配置生成”这类核心能力。

HaiWang 当前的定位不是“大而全面板”，而是一个更小、更直接的控制层：

- 用 SQLite 管账号、节点、流量事件、审计日志
- 生成 sing-box 服务端配置与客户端配置
- 对不同客户端输出更贴近原生体验的订阅内容
- 管理 sing-box 运行时、计费汇总、统计采集与证书

---

## 管理面板预览

> 下图为本地演示环境截图，使用示例数据展示账号、节点、运行时、计费与审计面板布局。

![HaiWang Dashboard](docs/assets/dashboard-overview.png)

---

## 文档导航

- 快速开始：本文档下方 `快速开始`
- 部署示例：[单机 systemd 部署示例](docs/deployment/single-node-systemd.md)
- 示例配置：[haiwang.example.json](docs/examples/haiwang.example.json)
- 贡献指南：[CONTRIBUTING.md](CONTRIBUTING.md)
- 开源协议：[MIT License](LICENSE)

---

## 当前能力

- 账号模型：`users` 为账号层，`user_nodes` 为节点层
- 多节点聚合：一个账号可以挂多个节点，订阅按账号聚合输出
- 多地区 / 多入口模板：节点支持 `protocol + region + entry + template` 元数据
- 协议支持：
  - `vless-reality`
  - `hysteria2`
- 订阅输出：
  - `sing-box` 聚合 profile
  - `Shadowrocket` 分享链接文本
  - `Stash` 完整 YAML / provider-only YAML
  - `Mihomo / Clash / FlClash` 完整 YAML / provider-only YAML
- 自托管规则集：
  - `/subscribe/{token}/rules/private.yaml`
  - `/subscribe/{token}/rules/direct.yaml`
- 运行时管理：
  - 生成服务端配置
  - 启动 / 重载 / 停止 sing-box
  - Hysteria2 证书状态查看与自签证书生成
- 流量与统计：
  - 手动上报流量事件
  - 异步计费汇总
  - 基于 V2Ray API 的统计采集
  - 失败退避与状态可视化
- 审计：
  - 管理接口审计日志

---

## 适合谁

如果你需要的是下面这些能力，HaiWang 比“传统大面板”更合适：

- 想自己掌控 sing-box 配置生成逻辑
- 想做多协议、多节点、按账号聚合的订阅分发
- 想把控制层写得足够薄，便于继续二次开发
- 想在本地或小规模环境快速跑通一套代理控制平面

如果你要的是成熟商用的多租户计费、复杂权限体系、完整运营后台，这个仓库目前还在 MVP 到增强版的阶段。

---

## 快速开始

### 1. 环境要求

- Go 1.22+
- 可执行的 `sing-box`
- Linux / macOS / Windows 均可

### 2. 安装依赖

```bash
go mod download
```

### 3. 初始化

```bash
go run . init
```

初始化后会生成或准备好：

- `haiwang.json`
- `data/haiwang.db`
- `profiles/`
- `runtime/sing-box.server.json`
- `runtime/sing-box.log`
- Hysteria2 自签证书与私钥（默认写入 `runtime/`）

同时会自动补齐：

- REALITY 密钥对
- REALITY `short_id`
- 管理令牌
- Hysteria2 自签证书

### 4. 启动服务

```bash
go run . run
```

默认监听：

```text
http://127.0.0.1:8080
```

浏览器直接访问首页即可打开内置管理面板。

---

## 部署示例

如果你准备把 HaiWang 作为单机控制面部署到 Linux 服务器，可以从下面两个文件开始：

- [单机 systemd 部署示例](docs/deployment/single-node-systemd.md)
- [示例配置文件](docs/examples/haiwang.example.json)

这套示例覆盖二进制部署、初始化、`systemd` service、配置落盘和上线前检查项，适合作为第一版生产化参考。

---

## 常用命令

```bash
go run . init
go run . run
go run . reality-keygen
go run . admin-token
go run . hysteria2-certgen
```

说明：

- `init`：初始化配置、数据库、令牌、REALITY 密钥和 Hysteria2 证书
- `run`：启动 HTTP 服务
- `reality-keygen`：重新生成 REALITY 密钥对
- `admin-token`：重新生成管理令牌
- `hysteria2-certgen`：重新生成 Hysteria2 自签证书

---

## 配置概览

主配置文件为 `haiwang.json`。

最关键的字段是：

### `server`

- `address`：HTTP 服务监听地址

### `runtime`

- `sing_box_binary`：`sing-box` 可执行文件路径
- `log_path`：运行日志路径

### `core`

- `domain`：订阅与节点输出使用的主域名
- `subscribe_path`：订阅根路径，默认 `/subscribe`
- `client_output_dir`：生成客户端配置文件目录
- `server_config_path`：服务端 sing-box 配置输出路径

### `core.reality`

- `server_name`
- `private_key`
- `public_key`
- `short_id`

### `core.hysteria2`

```json
{
  "listen_port": 8443,
  "up_mbps": 100,
  "down_mbps": 100,
  "obfs_password": "",
  "tls_cert_path": "runtime/hysteria2.server.crt",
  "tls_key_path": "runtime/hysteria2.server.key",
  "allow_insecure": true
}
```

### `core.entry_points`

用于定义不同“入口”的域名、端口和 SNI 组合。

```json
[
  {
    "name": "default",
    "domain": "example.com",
    "vless_port": 443,
    "reality_server_name": "www.cloudflare.com",
    "hysteria2_port": 8443,
    "hysteria2_server_name": "example.com"
  },
  {
    "name": "edge-hk",
    "domain": "hk.example.com",
    "vless_port": 2443,
    "reality_server_name": "www.cloudflare.com",
    "hysteria2_port": 9443,
    "hysteria2_server_name": "hk.example.com"
  }
]
```

每个入口还可以覆盖 Hysteria2 相关参数，例如：

```json
{
  "name": "edge-hk",
  "domain": "hk.example.com",
  "vless_port": 2443,
  "reality_server_name": "www.cloudflare.com",
  "hysteria2_port": 9443,
  "hysteria2_server_name": "hk.example.com",
  "hysteria2": {
    "up_mbps": 200,
    "down_mbps": 200,
    "obfs_password": "hk-obfs",
    "cert_mode": "self-signed",
    "allow_insecure": false
  }
}
```

当前支持的 Hysteria2 入口级证书模式：

- `global`
  - 复用全局 `core.hysteria2.tls_cert_path` / `tls_key_path`
- `self-signed`
  - 由 HaiWang 为该入口自动生成并管理独立证书
- `custom`
  - 使用入口自己显式指定的证书路径

入口级 Hysteria2 当前可覆盖：

- `up_mbps`
- `down_mbps`
- `obfs_password`
- `allow_insecure`
- `cert_mode`
- `tls_cert_path`
- `tls_key_path`

### `core.node_templates`

用于定义“地区 + 入口 + 协议集合”的模板。

```json
[
  {
    "name": "global-default",
    "region": "global",
    "entry": "default",
    "protocols": ["vless-reality", "hysteria2"]
  },
  {
    "name": "hk-edge",
    "region": "hk",
    "entry": "edge-hk",
    "protocols": ["vless-reality"]
  }
]
```

### `stats`

```json
{
  "enabled": true,
  "v2ray_api_listen": "127.0.0.1:18080",
  "collect_interval_seconds": 15,
  "reset_after_collect": true,
  "failure_backoff_max_seconds": 300,
  "alert_failure_threshold": 3
}
```

---

## 数据模型

当前仓库已经从“一个用户只有一个协议节点”演进为“账号 + 节点”的二层模型。

### 账号层：`users`

- 订阅 token
- 配额与已用流量
- 到期时间
- 账号启用状态

### 节点层：`user_nodes`

- 所属账号
- 协议类型
- 节点 token
- 地区（`region`）
- 入口（`entry`）
- 模板来源（`template_name`）
- 节点启用状态

这意味着：

- 一个账号可以同时拥有 `vless-reality` 和 `hysteria2` 节点
- 一个账号也可以同时拥有不同地区、不同入口下的同协议节点
- 订阅按账号聚合输出全部活跃节点
- 计费按账号聚合，而不是按单节点拆账
- 旧数据会自动迁移为“一个账号 + 一个默认节点”

---

## 订阅能力

### 基础订阅

```text
GET /subscribe/{token}
```

根据客户端 `User-Agent` 自动分发为不同格式：

- `sing-box`：聚合 profile
- `Shadowrocket`：分享链接文本
- `Stash`：完整 YAML
- `Mihomo / Clash / FlClash`：完整 YAML

### Provider-only 模式

```text
GET /subscribe/{token}?mode=provider
GET /subscribe/{token}?mode=provider&client=stash
GET /subscribe/{token}?mode=provider&client=mihomo
```

行为：

- `mode=provider` 返回顶层 `proxies:` YAML
- `client=stash` 强制输出 Stash 风格字段
- `client=mihomo` / `client=clash` / `client=flclash` 强制输出 Mihomo 风格字段
- 未显式指定 `client` 时，仍优先按 `User-Agent` 自动判断

### 内置 Rule Provider

```text
GET /subscribe/{token}/rules/private.yaml
GET /subscribe/{token}/rules/direct.yaml
```

当前模板行为：

- `private`：本地与私网流量走 `DIRECT`
- `direct`：`GEOIP,CN` 与 `DOMAIN-SUFFIX,cn` 走 `DIRECT`
- 其余流量走 `PROXY`

### 订阅头部

服务端会附带这些头部，方便 Clash 生态客户端展示配额信息：

- `Subscription-Userinfo`
- `Profile-Update-Interval`
- `Content-Disposition`

---

## 账号与节点管理

### 创建单协议账号

```bash
curl -X POST http://127.0.0.1:8080/api/users \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "test-user",
    "protocol": "vless-reality",
    "quota_bytes": 10737418240
  }'
```

### 创建多协议账号

```bash
curl -X POST http://127.0.0.1:8080/api/users \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "bundle-user",
    "protocols": ["vless-reality", "hysteria2"],
    "quota_bytes": 10737418240
  }'
```

### 按模板创建账号

```bash
curl -X POST http://127.0.0.1:8080/api/users \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "regional-user",
    "template_names": ["global-default", "hk-edge"],
    "quota_bytes": 10737418240
  }'
```

### 给已有账号追加节点

```bash
curl -X POST http://127.0.0.1:8080/api/users/1/nodes \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"protocol":"hysteria2"}'
```

### 以“协议 + 地区 + 入口”方式追加节点

```bash
curl -X POST http://127.0.0.1:8080/api/users/1/nodes \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "protocol": "vless-reality",
    "region": "hk",
    "entry": "edge-hk"
  }'
```

### 通过模板追加节点

```bash
curl -X POST http://127.0.0.1:8080/api/users/1/nodes \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"template_name":"hk-edge"}'
```

### Web 面板模板选择

内置管理面板现在支持可视化模板选择：

- 创建账号时可以直接勾选模板
- 账号节点列表会显示每个节点的 `region / entry / template`
- 账号操作区会直接给出可追加的模板按钮
- 模板卡片会显示协议集合、显示名模式、分组标签和默认启用信息

### 节点级操作

```text
POST   /api/users/{id}/nodes/{node_id}/enable
POST   /api/users/{id}/nodes/{node_id}/disable
DELETE /api/users/{id}/nodes/{node_id}
```

约束：

- 不允许删除账号下最后一个节点
- 如果要完全删除账号，请直接删除账号本身
- 同一账号内，节点按 `protocol + region + entry` 去重

### 账号级操作

```text
GET    /api/users
POST   /api/users
POST   /api/users/{id}/profile
POST   /api/users/{id}/enable
POST   /api/users/{id}/disable
DELETE /api/users/{id}
GET    /api/users/{id}/usage
```

---

## 运行时与证书接口

### 运行时

```text
GET  /api/runtime/status
POST /api/runtime/start
POST /api/runtime/reload
POST /api/runtime/stop
```

### Hysteria2 证书

```text
GET  /api/runtime/certs/hysteria2/status
POST /api/runtime/certs/hysteria2/generate
```

示例：

```bash
curl -X POST http://127.0.0.1:8080/api/runtime/certs/hysteria2/generate \
  -H "X-Admin-Token: <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"force": true}'
```

行为：

- `init` 会在证书不存在时自动生成自签证书
- `hysteria2-certgen` 会强制重签
- `self-signed` 入口会自动生成并管理入口级证书
- 重新生成证书后会尝试同步 runtime，让运行中的 sing-box 尽快吃到新证书

---

## 流量、统计与审计接口

```text
POST /api/usage/report
GET  /api/billing/status
POST /api/billing/flush
GET  /api/stats/status
POST /api/stats/collect
GET  /api/audit/logs
```

说明：

- 流量事件先写入 `usage_events`
- 后台异步汇总到账户流量
- 统计采集支持失败退避和状态暴露
- 所有管理动作都会写审计日志

---

## 项目结构

```text
HaiWang/
├── api/         # HTTP API 与内嵌 Web 管理面板
├── config/      # 配置加载、保存、默认值与初始化
├── core/        # 订阅生成、运行时配置、证书、规则集
├── database/    # SQLite schema、账号/节点/流量/审计读写
├── docs/        # 截图、部署示例与示例配置
├── middleware/  # 客户端识别
├── CONTRIBUTING.md
├── LICENSE
├── main.go      # CLI 入口
└── go.mod
```

---

## 当前状态

这个仓库已经不是“只有概念”的原型，而是一个能跑起来、能创建账号、能发订阅、能启动 sing-box、能做多节点聚合的控制平面 MVP。

但它仍然是一个偏工程化的基础项目，而不是已经打磨完毕的商用平台。当前还缺少的典型能力包括：

- 更细的权限模型（RBAC）
- 更完整的地区/入口模板参数体系
- 更成熟的节点生命周期管理
- 更丰富的规则模板与策略模板
- 更完整的生产部署与监控文档

---

## 安全与使用说明

本项目仅适合在遵守当地法律法规的前提下，用于网络技术研究、学习和合法授权场景。

如果你计划把它用在公开服务或生产环境，请至少补齐：

- TLS / 证书管理策略
- 配置备份与密钥轮换
- 权限控制
- 日志与监控
- 审计与访问限制

---

## 参与贡献

欢迎通过 Issue 或 Pull Request 一起完善 HaiWang。开始前建议先阅读 [CONTRIBUTING.md](CONTRIBUTING.md)，里面补充了本地开发、兼容性要求、迁移注意点以及提交前自检清单。

---

## 开源协议

本项目当前采用 [MIT License](LICENSE)。

---

## Roadmap

建议优先级较高的下一步：

- 节点模板参数化生成
- 入口级证书 / 域名 / 策略细分
- 更完整的 Clash / Stash 规则模板
- 节点级健康检查与自动摘除
- 更多生产部署样例与监控文档


-----

## 🛡️ 安全声明

本工具仅供网络技术研究与开发使用，请在遵守当地法律法规的前提下使用。

-----

## 🤝 贡献

如果你有更好的高伪装想法或协议优化方案，欢迎提交 Pull Request。

-----

## 📝 核心哲学

> **海纳百川，无迹可寻。**
> 连接，本该如此自由。

-----
