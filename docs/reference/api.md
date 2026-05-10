# API Appendix

This chapter is placed at the end of the handbook for integrators and advanced users. If you are only using Signal Horse through the UI, you can skip this page at first.

If you are already comfortable with the interface and want to do script integration, secondary development, or external AI tool integration, continue with the API notes below.

## Base address

Default local address:

```text
http://127.0.0.1:38182
```

Common entry points:

- `GET /`: currently acts as the UI entry and also as a health alias.
- `GET /health`: service health check.

## Authentication and account model

Write requests and private read requests usually work in one of two ways:

1. Pass `account_id`
2. Pass `api_key`, `secret_key`, and `passphrase` directly

### Common fields

| Field | Meaning |
| --- | --- |
| `exchange` | `okx` / `binance` / `bitget` / `gate` / `bybit` |
| `asset_type` | `spot` or `swap` |
| `symbol` | Such as `BTCUSDT` or `BTC/USDT` |
| `testnet` | `true` uses testnet; omitted or `false` uses live |
| `account_id` | Use a previously saved account |
| `api_key` | Pass API key directly |
| `secret_key` | Pass secret directly |
| `passphrase` | Required by OKX / Bitget |

## Endpoint groups

### Service and health

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/` | Currently serves both as the UI entry and a health alias |
| GET | `/health` | Whether the service is available |

### Private read APIs

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/positions` | Read swap positions |
| GET | `/balances` | Read balances |
| GET | `/orders-history` | Read historical orders |
| GET | `/positions-history` | Read historical positions |
| GET | `/open-orders` | Read current open orders |
| GET | `/open-tpsl-orders` | Read current TP / SL orders |

### Trading write APIs

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/place-order` | Main order placement entry |
| POST | `/order` | Legacy alias kept for compatibility |
| POST | `/cancel-order` | Cancel a normal order |
| POST | `/close-all` | Close positions / liquidate holdings |
| POST | `/set-leverage` | Set leverage |
| POST | `/set-margin-mode` | Set margin mode |
| POST | `/set-tpsl` | Set TP / SL |
| POST | `/cancel-tpsl` | Cancel TP / SL |

### Accounts and local settings

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/accounts` | List locally saved accounts |
| POST | `/accounts` | Save an account |
| PUT | `/accounts/:id` | Update an account |
| DELETE | `/accounts/:id` | Delete an account |
| POST | `/accounts/test` | Test credentials without saving |
| GET | `/accounts/:id/test` | Test a saved account |
| GET | `/settings/ai` | Read AI settings |
| POST | `/settings/ai` | Save AI settings |

### Public market data

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/market/symbols` | Tradable symbol list |
| GET | `/market/ticker` | One symbol ticker |
| GET | `/market/tickers` | Batch ticker endpoint |
| GET | `/market/klines` | Kline data |
| WS | `/ws/klines` | Real-time kline proxy |

### AI proxy

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/ai/proxy` | Proxy a chat completion request to the configured AI provider |

## Example: health check

```bash
curl -fsS http://127.0.0.1:38182/health
```

## Example: read balances

If you use a saved account, prefer the `account_id` style.

```text
GET /balances?exchange=bybit&account_id=<saved-account-id>&testnet=true
```

If you are only doing temporary integration testing, you can pass credentials directly, but that is better kept to a trusted local environment.

## Example: place a market order

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

### Order field notes

| Field | Common? | Description |
| --- | --- | --- |
| `symbol` | Yes | Trading pair |
| `asset_type` | Yes | `spot` or `swap` |
| `side` | Yes | `buy` or `sell` |
| `quantity` | Yes | Order quantity |
| `quantity_unit` | Recommended | In swap workflows, explicitly using `base` is recommended for coin quantity |
| `leverage` | Common for swaps | Defaults to `1` |
| `margin_mode` | Optional for swaps | `cross` / `isolated` |
| `position_side` | Common in hedge mode | `long` / `short` |
| `action` | Common for swaps | `open` / `close` |
| `order_type` | Common | `market` / `limit` |
| `price` | Required for limit orders | Limit price |
| `trigger_price` | Common for trigger orders | Trigger price |
| `trigger_direction` | Common for trigger orders | `above` / `below` |
| `testnet` | Strongly recommended | Validate on testnet first |

## Example: set TP / SL

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

### TP / SL request fields

| Field | Description |
| --- | --- |
| `symbol` | Trading pair |
| `asset_type` | `spot` / `swap` |
| `side` | The protective side required for the current position |
| `quantity` | Protected quantity, optional |
| `take_profit_price` | Take-profit trigger price |
| `stop_loss_price` | Stop-loss trigger price |
| `position_side` | In hedge mode, specify `long` or `short` |
| `account_id` or direct credentials | Choose one |
| `testnet` | Whether to use testnet |

## Integration suggestions

!!! tip "Recommended local integration order"
    1. Call `/health` first.
    2. Then call `/balances`, `/positions`, and `/orders-history` for read-only confirmation.
    3. Only after that should you use write endpoints such as `/place-order` and `/set-tpsl`.
    4. When possible, use `account_id` instead of repeatedly sending raw credentials.