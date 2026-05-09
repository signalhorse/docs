# 添加账户

Signal Horse 支持两种凭据使用方式：

1. 先把账户保存到本地，再通过 `account_id` 调用。
2. 在请求中直接传入 `api_key`、`secret_key`、`passphrase`。

如果你是长期使用同一台机器，推荐第一种。如果你是临时脚本或一次性联调，第二种更直接。

## 支持的交易所与字段差异

| 交易所 | 是否需要 `passphrase` | 说明 |
| --- | --- | --- |
| OKX | 是 | 建议先用模拟盘验证联通性 |
| Binance | 否 | 现货测试网和合约测试网是不同体系 |
| Bybit | 否 | 支持 demo / testnet |
| Bitget | 是 | 当前更适合使用 UTA 账户 |
| Gate.io | 否 | 现货和合约余额池表现可能共享 |

## 创建 API Key 时的建议

- 不要开启提现权限。
- 先只给最小可用交易权限。
- 首次接入优先使用测试网或模拟盘。
- 如果交易所对现货和合约使用不同测试网凭据，不要混用。

## 在 UI 里添加账户

推荐流程：

1. 进入本地 UI。
2. 打开账户管理区域。
3. 选择交易所。
4. 填写 `API Key` 和 `Secret`。
5. 对 `OKX` 或 `Bitget` 再填写 `Passphrase`。
6. 选择当前账户是实盘还是测试网。
7. 先执行一次“测试连接”或读取余额验证。

## 使用保存后的 `account_id`

保存账户后的好处是，后续调用本地接口时可以不再反复传原始密钥，而是直接传 `account_id`。

例如写请求可以采用这种形式：

```json
{
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
}
```

## 直接传密钥的方式

如果你不想先保存账户，也可以直接在请求里传：

```json
{
  "exchange": "okx",
  "symbol": "BTCUSDT",
  "asset_type": "swap",
  "side": "buy",
  "action": "open",
  "quantity": 0.001,
  "quantity_unit": "base",
  "leverage": 2,
  "order_type": "market",
  "api_key": "<api-key>",
  "secret_key": "<secret-key>",
  "passphrase": "<passphrase>",
  "testnet": true
}
```

## 测试网与实盘的切换原则

当前项目约定里：

- `testnet=true` 表示走测试网 / 模拟盘。
- `testnet=false` 或省略该字段，表示走实盘。

!!! warning "不要假设一套凭据能同时覆盖所有环境"
    某些交易所会把现货测试网、合约测试网、实盘 API 分成不同凭据体系。只要读取接口报权限错误，先检查是不是环境和密钥不匹配，而不是先怀疑前端页面。

## 添加账户后的检查清单

添加完账户后，请至少做下面三件事：

1. 读取余额。
2. 读取持仓。
3. 读取历史订单或历史仓位。

全部通过后，再进入 [手动交易](manual-trading.md)。