# Signal Horse 用户手册

Signal Horse 是一个本地优先的加密交易工作台。它把本地浏览器 UI、本地 HTTP API、账户管理、手动下单、TP/SL、AI 分析和自动化交易放在同一套运行环境里，默认通过 `http://127.0.0.1:38182/` 提供服务。

!!! tip "建议阅读顺序"
    1. 先看 [安装部署](getting-started/installation.md)，把程序运行起来。
    2. 再看 [首次启动](getting-started/first-run.md)，确认服务健康和页面入口。
    3. 然后看 [添加账户](guides/add-account.md)，优先用测试网或模拟盘完成联通性检查。
    4. 最后再看 [手动交易](guides/manual-trading.md) 和 [AI 与自动化](guides/ai-automation.md)。

## 这套系统能做什么

- 在本机或同一局域网内打开统一交易界面。
- 接入 `OKX`、`Binance`、`Bybit`、`Bitget`、`Gate.io`。
- 同时支持 `spot` 和 `swap` 两类资产场景。
- 支持保存账户后使用 `account_id` 调用，也支持直接传入 `api_key`、`secret_key`、`passphrase`。
- 通过 `testnet=true` 将请求路由到支持的测试网或模拟盘环境。

## 快速事实

| 项目 | 当前约定 |
| --- | --- |
| 本地 UI 地址 | `http://127.0.0.1:38182/` |
| 健康检查 | `GET /health` |
| 根路径行为 | `GET /` 当前同时作为 UI 入口和健康别名 |
| Windows 主发布物 | `signal_horse_windows_portable.zip` |
| Windows 启动脚本 | `Start Signal Executor.cmd` |
| Linux / macOS 一键安装 | `https://signal.horse/install.sh` |
| Windows 一键安装 | `https://signal.horse/install.ps1` |

## 适合谁使用

- 想在自己的电脑上运行交易工具，而不是把密钥发到远程服务的用户。
- 想通过浏览器进行手动交易和状态查看的操作者。
- 想把本地执行器接给 OpenClaw、Claude Code、Codex 或其他 AI 工作流的人。

## 文档范围

这套文档聚焦于“怎么安装、怎么启动、怎么配置账户、怎么使用 UI、怎么调用本地 API”。

它不覆盖：

- 每个交易所适配器的底层实现细节。
- Windows / macOS / Linux 打包脚本内部实现。
- 站点后台、分析面板或管理侧逻辑。

如果你需要对接接口字段，请直接跳到 [API 附录](reference/api.md)。

## 文档地图

如果你是普通使用者，建议按这条路径阅读：

1. [安装部署](getting-started/installation.md)
2. [首次启动](getting-started/first-run.md)
3. [界面导览](guides/ui-tour.md)
4. [添加账户](guides/add-account.md)
5. [手动交易](guides/manual-trading.md)
6. [AI 与自动化](guides/ai-automation.md)
7. [更新与维护](guides/update-maintenance.md)

## 当前手册已经覆盖的主题

- 安装和首启路径
- UI 主界面导览
- 账户接入和测试网使用
- 手动下单、TP/SL 与清理动作
- AI 分析和 Bot 自动化
- 常见问题与本地 API 附录
