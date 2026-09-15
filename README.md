<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="./brand/arkret-logo-dark.svg" />
    <img src="./brand/arkret-logo.svg" alt="Arkret logo" width="160" />
  </picture>
</p>

# Arkret

Arkret 是面向个人、组织与 AI Agent 的开放协作协议及其实现：各方可以自托管 Account Station 并跨域协作，
每个 Realm 由单一 current governance Station 接纳 Event、签发 RealmCommit；Realm、每个 Circle 与每个
Agent Sidecar 各自维护独立 commit stream，不存在跨 stream 总序。协议以这些 scope 划分访问与 MLS
端到端加密边界，同时把聊天、任务、文档、日历和通话统一为可由不同客户端投影的协作模型。

- 协议站：https://arkret.org
- 规范本体（唯一真相源）：[`arkret-spec`](https://github.com/arkret-org/arkret-spec)

> 当前处于开发阶段，尚无正式发布；协议与实现采取激进更新，不保证兼容旧数据。

## 仓库地图

本组织由一组同级独立仓库构成，不存在 monorepo。依赖流向统一为：
`arkret-spec` → `arkret-rust-sdk` → 各服务 / 客户端 → `cotest`。

### 协议与 SDK

| 仓库 | 角色 |
| --- | --- |
| [arkret-spec](https://github.com/arkret-org/arkret-spec) | 协议规范唯一真相源。normative 正文在 `spec/v1/zh/`，机器构件（registry / JSON Schema / OpenAPI / fixtures）在 `spec/v1/artifacts/`，同时承载协议站源码 |
| [arkret-rust-sdk](https://github.com/arkret-org/arkret-rust-sdk) | 共享 Rust SDK：wire 类型、校验器、canonical JSON / digest 助手、HTTP client、conformance 助手。spec 中定义的类型只允许在此实现，供其他仓库复用 |

### 服务端

| 仓库 | 角色 |
| --- | --- |
| [soland](https://github.com/arkret-org/soland) | 主服务器实现：事件摄入、账户聚合与同步、授权投影、密钥备份、联邦、媒体等 |
| [coauth](https://github.com/arkret-org/coauth) | 账户与认证支持：OIDC、DID 绑定、账户生命周期，面向组织部署的权限与管理 |
| [floria](https://github.com/arkret-org/floria) | 推送网关：provider 分发、推送规则、可观测性 |
| [teabay](https://github.com/arkret-org/teabay) | 目录服务：发现、收录与查询、反枚举、下架审计、时效性维护 |

### 客户端与 UI

| 仓库 | 角色 |
| --- | --- |
| [garth](https://github.com/arkret-org/garth) | 运行时中立的共享客户端引擎（L2）：run loop、持久游标 / 去重 / 收件箱、出站队列；不含 UI 框架，以 path 依赖被 inkson 消费 |
| [inkson](https://github.com/arkret-org/inkson) | 跨平台客户端（Dioxus，桌面 / 移动 / Web），承载用户侧 E2EE / MLS 行为 |
| [sodmin](https://github.com/arkret-org/sodmin) | 服务器管理 UI，基于 soland / coauth API |
| [chime](https://github.com/arkret-org/chime) | 通知集成层，连接 floria 与客户端 SDK |

### 集成与测试

| 仓库 | 角色 |
| --- | --- |
| [bridges](https://github.com/arkret-org/bridges) | 第三方聊天桥接 applet（Discord、Telegram、Slack、飞书、微信等），每个应用一个 crate，共享 runtime / messaging 两个基础 crate |
| [cotest](https://github.com/arkret-org/cotest) | 联合端到端 / 一致性测试套件（类似 Matrix Complement），真实服务与实时协议测试归属此仓库 |

### 相关仓库

- `yoface` — 共享 Dioxus 组件库（私有仓库），inkson / sodmin 以 cargo git 依赖消费。
- `savfox` — 生态相关项目，位于 `savfox-ai` 组织，不属于本工作区。

## 协作规则（摘要）

- **`arkret-spec` 是唯一真相源**：任何实现与规范的漂移都是实现侧 bug；协议问题先记录、修正并验证。
- 规范中出现的类型定义只能在 `arkret-rust-sdk` 实现，其余仓库禁止自行重复定义。
- 服务间只访问 spec 定义的 `/_arkret/...` 标准接口，不引入实现私有的 wire 接口。
- 协议版本固定为 v1，不引入其他自定义版本 profile 或存储键。
