# API 附录

这一章放在手册最后，面向集成方和高级使用者。普通用户如果只是通过 UI 使用 Signal Horse，可以先跳过本页。

如果你已经熟悉了界面操作，又准备做脚本联调、二次开发或外部 AI 工具接入，再继续看下面的接口说明。

## 基本地址

默认本地地址：

```text
http://127.0.0.1:38182
```

常用入口：

- `GET /`：当前可作为 UI 入口，也兼容健康别名。
- `GET /health`：服务健康检查。

## 认证与账户模型

写请求和私有读请求通常有两种方式：

1. 传 `account_id`
2. 直接传 `api_key`、`secret_key`、`passphrase`

### 当前常用字段

| 字段 | 含义 |
| --- | --- |
| `exchange` | `okx` / `binance` / `bitget` / `gate` / `bybit` |
| `asset_type` | `spot` 或 `swap` |
| `symbol` | 例如 `BTCUSDT` 或 `BTC/USDT` |
| `testnet` | `true` 走测试网，省略或 `false` 走实盘 |
| `account_id` | 使用已保存账户 |
| `api_key` | 直接传 API Key |
| `secret_key` | 直接传 Secret |
| `passphrase` | OKX / Bitget 需要 |

## 端点分组

### 服务与健康

| 方法 | 端点 | 说明 |
| --- | --- | --- |
| GET | `/` | 当前同时承担 UI 入口和健康别名 |
| GET | `/health` | 服务是否可用 |

### 私有读接口

| 方法 | 端点 | 说明 |
| --- | --- | --- |
| GET | `/positions` | 读取合约持仓 |
| GET | `/balances` | 读取余额 |
| GET | `/orders-history` | 读取历史订单 |
| GET | `/positions-history` | 读取历史仓位 |
| GET | `/open-orders` | 当前挂单 |
| GET | `/open-tpsl-orders` | 当前 TP / SL 订单 |

### 交易写接口

| 方法 | 端点 | 说明 |
| --- | --- | --- |
| POST | `/place-order` | 下单主入口 |
| POST | `/order` | 旧别名，兼容保留 |
| POST | `/cancel-order` | 撤销普通订单 |
| POST | `/close-all` | 平仓 / 清仓 |
| POST | `/set-leverage` | 设置杠杆 |
| POST | `/set-margin-mode` | 设置保证金模式 |
| POST | `/set-tpsl` | 设置 TP / SL |
| POST | `/cancel-tpsl` | 取消 TP / SL |

### 账户与本地设置

| 方法 | 端点 | 说明 |
| --- | --- | --- |
| GET | `/accounts` | 列出本地已保存账户 |
| POST | `/accounts` | 保存账户 |
| PUT | `/accounts/:id` | 更新账户 |
| DELETE | `/accounts/:id` | 删除账户 |
| POST | `/accounts/test` | 测试凭据但不保存 |
| GET | `/accounts/:id/test` | 测试已保存账户 |
| GET | `/settings/ai` | 读取 AI 设置 |
| POST | `/settings/ai` | 保存 AI 设置 |

### 公共市场数据

| 方法 | 端点 | 说明 |
| --- | --- | --- |
| GET | `/market/symbols` | 可交易 symbol 列表 |
| GET | `/market/ticker` | 单个 symbol 行情 |
| GET | `/market/tickers` | 批量 ticker |
| GET | `/market/klines` | K 线数据 |
| WS | `/ws/klines` | 实时 K 线代理 |

### AI 代理

| 方法 | 端点 | 说明 |
| --- | --- | --- |
| POST | `/ai/proxy` | 把聊天补全请求代理到配置好的 AI 提供方 |

## 示例：健康检查

```bash
curl -fsS http://127.0.0.1:38182/health
```

## 示例：读取余额

如果你使用保存后的账户，可以优先使用 `account_id` 风格。

```text
GET /balances?exchange=bybit&account_id=<saved-account-id>&testnet=true
```

如果你只是临时联调，也可以直接传凭据，但更适合在本机安全环境中使用。

## 示例：下市价单

```bash
curl -X POST http://127.0.0.1:38182/place-order \
  -H "Content-Type: application/json" \
  -d '{
    "exchange": "bybit",
    "symbol": "BTCUSDT",
    "asset_type": "swap",
    "side": "buy",
    "action": "open",
    "quantity": 0.001,
    "quantity_unit": "base",
    "leverage": 3,
    "order_type": "market",
    "account_id": "<saved-account-id>",
    "testnet": true
  }'
```

### 下单字段说明

| 字段 | 是否常用 | 说明 |
| --- | --- | --- |
| `symbol` | 是 | 交易对 |
| `asset_type` | 是 | `spot` 或 `swap` |
| `side` | 是 | `buy` 或 `sell` |
| `quantity` | 是 | 下单数量 |
| `quantity_unit` | 推荐 | 合约场景建议显式用 `base` 表示币数量 |
| `leverage` | 合约常用 | 默认 `1` |
| `margin_mode` | 合约可选 | `cross` / `isolated` |
| `position_side` | 对冲模式常用 | `long` / `short` |
| `action` | 合约常用 | `open` / `close` |
| `order_type` | 常用 | `market` / `limit` |
| `price` | 限价单必填 | 限价价格 |
| `trigger_price` | 条件单常用 | 触发价 |
| `trigger_direction` | 条件单常用 | `above` / `below` |
| `testnet` | 强烈推荐 | 先用测试网验证 |

## 示例：设置 TP / SL

```bash
curl -X POST http://127.0.0.1:38182/set-tpsl \
  -H "Content-Type: application/json" \
  -d '{
    "exchange": "bybit",
    "symbol": "BTCUSDT",
    "asset_type": "swap",
    "side": "sell",
    "quantity": 0.001,
    "quantity_unit": "base",
    "take_profit_price": 72000,
    "stop_loss_price": 68000,
    "account_id": "<saved-account-id>",
    "testnet": true
  }'
```

### TP / SL 请求字段

| 字段 | 说明 |
| --- | --- |
| `symbol` | 交易对 |
| `asset_type` | `spot` / `swap` |
| `side` | 保护当前仓位所需的方向 |
| `quantity` | 保护数量，可选 |
| `take_profit_price` | 止盈触发价 |
| `stop_loss_price` | 止损触发价 |
| `position_side` | 对冲模式下指定 `long` 或 `short` |
| `account_id` 或直接密钥 | 二选一 |
| `testnet` | 是否走测试网 |

## 集成建议

!!! tip "本地接入的推荐方式"
    1. 先调用 `/health`。
    2. 再调用 `/balances`、`/positions`、`/orders-history` 做只读确认。
    3. 再执行 `/place-order`、`/set-tpsl` 等写接口。
    4. 能用 `account_id` 就尽量不要每次都重复传密钥。