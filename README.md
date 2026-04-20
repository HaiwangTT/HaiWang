# 🌊 HaiWang - 海王

**一个基于 Sing-box 核心的轻量化、高性能、高隐匿代理管理系统。**

[](https://www.google.com/search?q=LICENSE)
[](https://golang.org)
[](https://github.com/sagernet/sing-box)

`HaiWang`（海王）专为追求极致网络体验的极客设计。它摒弃了传统面板的臃肿，专注于核心的流量转发效率与深度指纹伪装，助你像大海之王一样，在复杂的网络环境中自由穿梭，连接万物。

-----

## ✨ 核心特性

  * **🚀 极轻量设计**：采用 Go 语言编写，原生二进制部署，不依赖 Java/Python 环境。
  * **⚡ 极速转发**：深度优化 Sing-box 内核配置，支持 Hysteria2、QUIC 等协议，大幅提升丢包环境下的吞吐量。
  * **🛡️ 高级伪装 (Stealth)**：
      * 内置 **REALITY** 证书动态模拟。
      * 支持 **uTLS** 指纹随机化，模拟主流浏览器特征。
      * 自动更换 ShortID，绕过深度包检测 (DPI)。
  * **🎭 智能订阅分发**：自研 UA 识别逻辑，一套订阅链接完美适配 **FlClash**, **Shadowrocket**, **Stash**, **Sing-box** 等全平台客户端。
  * **📊 稳健管理**：
      * 基于 SQLite 的高性能用户管理。
      * 异步流量计费系统，降低磁盘 I/O 损耗。
      * 支持到期自动熔断与流量限额。

-----

## 🛠️ 系统架构

HaiWang 采用控制平面与数据平面分离的设计：

1.  **控制平面 (Core API)**：管理用户、下发指令、统计流量。
2.  **数据平面 (Sing-box)**：高性能流量解密与转发。
3.  **适配层 (Adapter)**：根据客户端指纹动态生成配置。

-----

## 🚀 快速开始

### 1\. 环境准备

  * 操作系统：Linux (推荐 Ubuntu 22.04+ / Debian 11+)
  * 基础组件：Sing-box Binary

### 2\. 安装 (开发阶段)

```bash
# 克隆仓库
git clone https://github.com/your-username/HaiWang.git
cd HaiWang

# 编译项目
go build -o haiwang main.go

# 初始化配置
./haiwang init
```

### 3\. 运行

```bash
./haiwang run
```

-----

## 📁 项目目录结构

```text
HaiWang/
├── api/            # RESTful 接口实现
├── core/           # Sing-box 配置生成与内核控制
├── database/       # 用户数据与流量持久化 (SQLite)
├── middleware/     # 订阅识别与安全过滤
├── web/            # 极简可视化管理面板 (Vue/React)
└── main.go         # 程序入口
```

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
