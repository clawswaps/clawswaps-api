# WebSocket API

The WebSocket API provides real-time data streams for prices, orderbooks, and wallet updates on the Solana blockchain.

## Connection

Connect to our WebSocket server for real-time updates:

**WebSocket URL:** `wss://clawswaps.onrender.com`

### Connection Example

```javascript
const ws = new WebSocket('wss://clawswaps.onrender.com');

ws.onopen = () => {
  console.log('Connected to Clawap Exchange');
};

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Received:', data);
};

ws.onerror = (error) => {
  console.error('WebSocket error:', error);
};

ws.onclose = () => {
  console.log('Disconnected from Clawap Exchange');
};
```

### Python Example

```python
import websocket
import json

def on_message(ws, message):
    data = json.loads(message)
    print('Received:', data)

def on_error(ws, error):
    print('Error:', error)

def on_close(ws, close_status_code, close_msg):
    print('Disconnected')

def on_open(ws):
    print('Connected to Clawap Exchange')

    # Subscribe to price feed
    ws.send(json.dumps({
        'type': 'subscribe',
        'channel': 'prices',
        'tokens': ['SOL', 'USDC']
    }))

ws = websocket.WebSocketApp('wss://clawswaps.onrender.com',
                           on_message=on_message,
                           on_error=on_error,
                           on_close=on_close,
                           on_open=on_open)

ws.run_forever()
```

## Message Format

All WebSocket messages use JSON format:

```json
{
  "type": "message_type",
  "channel": "channel_name",
  "data": { ... }
}
```

## Channels

### Price Feed

Subscribe to real-time price updates for tokens.

#### Subscribe Message

```json
{
  "type": "subscribe",
  "channel": "prices",
  "tokens": ["SOL", "USDC", "BONK"]
}
```

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | string | Yes | Must be "subscribe" |
| channel | string | Yes | Must be "prices" |
| tokens | array | Yes | Array of token symbols or mint addresses |

#### Price Update Message

```json
{
  "type": "price_update",
  "channel": "prices",
  "data": {
    "token": "SOL",
    "mint": "So11111111111111111111111111111111111111112",
    "price": 156.04,
    "change24h": 2.5,
    "volume24h": 15000000,
    "high24h": 158.50,
    "low24h": 152.30,
    "timestamp": 1699564800000
  }
}
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| type | string | Always "price_update" |
| channel | string | Always "prices" |
| data.token | string | Token symbol |
| data.mint | string | Token mint address |
| data.price | number | Current price in USD |
| data.change24h | number | 24-hour price change percentage |
| data.volume24h | number | 24-hour trading volume |
| data.high24h | number | 24-hour high price |
| data.low24h | number | 24-hour low price |
| data.timestamp | number | Unix timestamp in milliseconds |

#### Unsubscribe Message

```json
{
  "type": "unsubscribe",
  "channel": "prices",
  "tokens": ["BONK"]
}
```

---

### Orderbook Stream

Subscribe to orderbook updates for a trading pair.

#### Subscribe Message

```json
{
  "type": "subscribe",
  "channel": "orderbook",
  "pair": "SOL/USDC",
  "depth": 20
}
```

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | string | Yes | Must be "subscribe" |
| channel | string | Yes | Must be "orderbook" |
| pair | string | Yes | Trading pair (e.g., "SOL/USDC") |
| depth | number | No | Orderbook depth (default: 10, max: 50) |

#### Orderbook Snapshot Message

Sent immediately after subscription:

```json
{
  "type": "orderbook_snapshot",
  "channel": "orderbook",
  "data": {
    "pair": "SOL/USDC",
    "bids": [
      [156.04, 10.5],
      [156.03, 5.2],
      [156.02, 8.7]
    ],
    "asks": [
      [156.05, 7.3],
      [156.06, 12.1],
      [156.07, 4.8]
    ],
    "timestamp": 1699564800000
  }
}
```

#### Orderbook Update Message

Sent when orderbook changes:

```json
{
  "type": "orderbook_update",
  "channel": "orderbook",
  "data": {
    "pair": "SOL/USDC",
    "side": "bid",
    "price": 156.04,
    "size": 10.5,
    "timestamp": 1699564800000
  }
}
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| type | string | "orderbook_update" |
| channel | string | "orderbook" |
| data.pair | string | Trading pair |
| data.side | string | "bid" or "ask" |
| data.price | number | Price level |
| data.size | number | New size at price level (0 means removed) |
| data.timestamp | number | Unix timestamp in milliseconds |

---

### Trades Stream

Subscribe to real-time trade executions.

#### Subscribe Message

```json
{
  "type": "subscribe",
  "channel": "trades",
  "pairs": ["SOL/USDC", "BONK/SOL"]
}
```

#### Trade Message

```json
{
  "type": "trade",
  "channel": "trades",
  "data": {
    "pair": "SOL/USDC",
    "price": 156.04,
    "size": 2.5,
    "side": "buy",
    "timestamp": 1699564800000,
    "signature": "5j7s6NiJS3JAkvgkoc18WVAsiSaci2pxB2A6ueCJP4tpr..."
  }
}
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| type | string | Always "trade" |
| channel | string | Always "trades" |
| data.pair | string | Trading pair |
| data.price | number | Trade execution price |
| data.size | number | Trade size |
| data.side | string | "buy" or "sell" |
| data.timestamp | number | Unix timestamp in milliseconds |
| data.signature | string | Solana transaction signature |

---

### Wallet Updates

Subscribe to updates for a specific wallet address.

#### Subscribe Message

```json
{
  "type": "subscribe",
  "channel": "wallet",
  "address": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU"
}
```

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | string | Yes | Must be "subscribe" |
| channel | string | Yes | Must be "wallet" |
| address | string | Yes | Solana wallet address to monitor |

#### Balance Update Message

```json
{
  "type": "balance_update",
  "channel": "wallet",
  "data": {
    "address": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
    "token": "SOL",
    "mint": "So11111111111111111111111111111111111111112",
    "balance": 5.23,
    "previousBalance": 5.13,
    "change": 0.10,
    "timestamp": 1699564800000
  }
}
```

#### Transaction Update Message

```json
{
  "type": "transaction",
  "channel": "wallet",
  "data": {
    "address": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
    "signature": "5j7s6NiJS3JAkvgkoc18WVAsiSaci2pxB2A6ueCJP4tpr...",
    "type": "swap",
    "status": "confirmed",
    "timestamp": 1699564800000
  }
}
```

---

### Ticker Stream

Subscribe to aggregated ticker data for multiple tokens.

#### Subscribe Message

```json
{
  "type": "subscribe",
  "channel": "ticker",
  "tokens": ["SOL", "USDC", "BONK"]
}
```

#### Ticker Message

Sent every second with aggregated data:

```json
{
  "type": "ticker",
  "channel": "ticker",
  "data": {
    "token": "SOL",
    "mint": "So11111111111111111111111111111111111111112",
    "last": 156.04,
    "open24h": 152.30,
    "high24h": 158.50,
    "low24h": 152.30,
    "volume24h": 15000000,
    "change24h": 2.5,
    "changePercent24h": 2.45,
    "timestamp": 1699564800000
  }
}
```

## Error Messages

WebSocket error messages follow this format:

```json
{
  "type": "error",
  "code": "INVALID_CHANNEL",
  "message": "Invalid channel name",
  "timestamp": 1699564800000
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| INVALID_CHANNEL | Channel name is invalid |
| INVALID_TOKEN | Token symbol or mint address is invalid |
| INVALID_PAIR | Trading pair format is invalid |
| SUBSCRIPTION_LIMIT | Maximum number of subscriptions reached |
| RATE_LIMIT | Message rate limit exceeded |
| AUTH_REQUIRED | Authentication required for this channel |

## Connection Management

### Ping/Pong

The server sends ping messages every 30 seconds:

```json
{
  "type": "ping",
  "timestamp": 1699564800000
}
```

Clients should respond with a pong message:

```json
{
  "type": "pong",
  "timestamp": 1699564800000
}
```

### Reconnection Strategy

Implement exponential backoff for reconnections:

```javascript
let reconnectDelay = 1000;
const maxReconnectDelay = 30000;

function connect() {
  const ws = new WebSocket('wss://clawswaps.onrender.com');

  ws.onclose = () => {
    console.log(`Reconnecting in ${reconnectDelay}ms...`);
    setTimeout(connect, reconnectDelay);
    reconnectDelay = Math.min(reconnectDelay * 2, maxReconnectDelay);
  };

  ws.onopen = () => {
    reconnectDelay = 1000; // Reset delay on successful connection
    // Resubscribe to channels
  };
}
```

## Rate Limits

WebSocket connections are subject to the following limits:

| Tier | Connections | Messages/second | Subscriptions |
|------|-------------|-----------------|---------------|
| Free | 1 | 10 | 10 |
| Pro | 5 | 100 | 100 |
| Enterprise | Unlimited | Unlimited | Unlimited |

## Best Practices

1. Implement automatic reconnection with exponential backoff
2. Respond to ping messages to keep connection alive
3. Resubscribe to channels after reconnection
4. Handle errors gracefully and log issues
5. Limit number of concurrent subscriptions
6. Buffer messages to handle bursts
7. Use message throttling on client side
8. Close connections when not needed

## Resources

- **Website:** [https://clawswaps.xyz](https://clawswaps.xyz)
- **GitHub:** [https://github.com/clawswaps/clawswaps-api](https://github.com/clawswaps/clawswaps-api)
- **Twitter:** [https://x.com/clawdswaps](https://x.com/clawdswaps)
