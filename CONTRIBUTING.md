# Contributing to HaiWang

感谢你愿意一起完善 HaiWang。

这个项目当前仍处在“快速演进，但要尽量保持兼容”的阶段，所以我们欢迎改进，同时也很在意已有数据模型、订阅输出和运行时链路不要被轻易打碎。

## 贡献方式

- 提交 Issue：报告 bug、补充使用反馈、提出协议/订阅模板建议
- 提交 Pull Request：修复问题、补文档、补测试、补部署样例
- 讨论设计：如果改动会影响数据结构、订阅格式或运行时行为，建议先开 Issue 对齐范围

## 本地开发

### 环境要求

- Go 1.22+
- 可执行的 `sing-box`
- 可写的本地工作目录

### 启动步骤

```bash
go mod download
go run . init
go run . run
```

默认启动后可访问：

```text
http://127.0.0.1:8080
```

首次初始化会生成：

- `haiwang.json`
- `data/haiwang.db`
- `profiles/`
- `runtime/`

## 提交前自检

提交前至少做这些检查：

- 运行 `gofmt`，保持代码风格一致
- 运行 `go test ./...`
- 运行 `go run . init`
- 运行 `go run . run` 并确认管理面板可访问

如果改动落在下面这些区域，请额外检查：

- 修改 `database/sqlite.go`
  需要考虑旧数据迁移路径，避免破坏现有库文件
- 修改 `/subscribe` 相关逻辑
  需要同时验证 `sing-box`、`Shadowrocket`、`Stash`、`Mihomo / Clash / FlClash`
- 修改 Hysteria2 服务端逻辑
  需要确认证书文件存在，且 runtime 配置重载链路仍可工作
- 修改配置、订阅、运行时相关逻辑
  需要同步更新 `README.md`，必要时补充部署示例
- 修改前端表格或字段结构
  需要同时检查 `api/web/index.html` 与 `api/web/assets/app.js`

## Pull Request 期望

PR 描述里建议包含以下内容：

- 改动目标和背景
- 是否涉及兼容性或迁移
- 手工验证或测试覆盖范围
- 是否同步更新了 README、部署示例或其他文档

## 提交风格建议

- 优先做兼容式演进，不要轻易采用会打碎现有数据的重构方案
- 小步提交，尽量让每个 PR 聚焦一个主题
- 文档改动同样有价值，欢迎单独提交

## License

提交代码即表示你同意该贡献以仓库当前使用的开源协议发布。当前协议见 [LICENSE](LICENSE)。
