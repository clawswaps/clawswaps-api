# Clawap Exchange API Documentation

Build powerful trading applications with our comprehensive REST and WebSocket APIs. Access real-time pricing, execute swaps, and manage wallets programmatically.

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Authentication](#authentication)
- [Rate Limits](#rate-limits)
- [Swap API](#swap-api)
- [Token API](#token-api)
- [Wallet API](#wallet-api)
- [WebSocket API](#websocket-api)
- [Resources](#resources)

## Introduction

The Clawap Exchange API provides developers with direct access to our decentralized exchange infrastructure built on the Solana blockchain. Our API offers the best rates and lowest slippage for token swaps.

**Base URL:** `https://clawswaps.onrender.com/api`

### Features

- Real-time token pricing and market data
- Optimal swap routing through decentralized liquidity sources
- WebSocket support for live price feeds
- Portfolio tracking and analytics
- Low latency infrastructure

## Getting Started

To get started with the Clawap Exchange API:

1. Review the documentation for your desired endpoints
2. Test the public endpoints without authentication
3. For production usage, obtain an API key from your dashboard
4. Integrate the API into your application

### Quick Example

```bash
curl https://clawswaps.onrender.com/api/tokens
```

## Authentication

Most endpoints are public and don't require authentication. However, for rate-limited access and premium features, you'll need an API key.

### Using an API Key

Include your API key in the `Authorization` header:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
     https://clawswaps.onrender.com/api/tokens
```

You can obtain an API key from your dashboard after connecting your wallet.

## Rate Limits

API rate limits are applied per IP address:

| Tier | Requests per minute | Requirements |
|------|---------------------|--------------|
| Free | 60 | No authentication |
| Pro | 600 | API key required |
| Enterprise | Unlimited | Contact sales |

## Swap API

### Get Quote

Get the best swap quote for a token pair with optimal routing.

**Endpoint:** `GET /swap/quote`

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| inputMint | string | Yes | Input token mint address |
| outputMint | string | Yes | Output token mint address |
| amount | number | Yes | Amount in smallest unit (lamports) |
| slippageBps | number | No | Slippage tolerance in basis points (default: 50) |

**Example Request:**

```bash
curl "https://clawswaps.onrender.com/api/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v&amount=1000000000&slippageBps=50"
```

**Example Response:**

```json
{
  "success": true,
  "quote": {
    "inputMint": "So11111111111111111111111111111111111111112",
    "outputMint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "inAmount": "1000000000",
    "outAmount": "156040000",
    "priceImpactPct": 0.12,
    "marketInfos": [
      {
        "id": "whirlpool",
        "label": "Whirlpool",
        "inputMint": "So11111111111111111111111111111111111111112",
        "outputMint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
        "inAmount": "1000000000",
        "outAmount": "156040000",
        "fee": {
          "amount": "25000",
          "mint": "So11111111111111111111111111111111111111112",
          "pct": 0.0025
        }
      }
    ]
  }
}
```

### Execute Swap

Execute a swap transaction based on a quote.

**Endpoint:** `POST /swap/execute`

**Request Body:**

```json
{
  "quoteResponse": { ... },
  "userPublicKey": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "wrapAndUnwrapSol": true
}
```

**Example Response:**

```json
{
  "success": true,
  "swapTransaction": "4hXTCkRzt9WyecN...",
  "lastValidBlockHeight": 150000000
}
```

### Swap History

Retrieve swap transaction history for a wallet address.

**Endpoint:** `GET /swap/history/:wallet`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| wallet | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| limit | number | No | Number of results (default: 50, max: 100) |
| offset | number | No | Pagination offset (default: 0) |

## Token API

### List Tokens

Get a list of all supported tokens with metadata.

**Endpoint:** `GET /tokens`

**Example Response:**

```json
{
  "success": true,
  "tokens": [
    {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "name": "Solana",
      "decimals": 9,
      "logoURI": "https://...",
      "verified": true,
      "volume24h": 15000000,
      "price": 156.04
    }
  ],
  "count": 150
}
```

### Token Info

Get detailed information about a specific token.

**Endpoint:** `GET /tokens/:mint`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| mint | string | Yes | Token mint address |

### Token Price

Get real-time prices for one or multiple tokens.

**Endpoint:** `GET /price`

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| ids | string | Yes | Comma-separated list of token mints |

**Example Request:**

```bash
curl "https://clawswaps.onrender.com/api/price?ids=So11111111111111111111111111111111111111112,EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"
```

### Trending Tokens

Get trending tokens based on volume and price action.

**Endpoint:** `GET /tokens/trending`

## Wallet API

### Wallet Balance

Get total portfolio balance in USD for a wallet.

**Endpoint:** `GET /wallet/:address/balance`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| address | string | Yes | Solana wallet address |

### Wallet Tokens

List all tokens held by a wallet with balances and values.

**Endpoint:** `GET /wallet/:address/tokens`

**Example Response:**

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "tokens": [
    {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "balance": 5.23,
      "decimals": 9,
      "usdValue": 816.23
    }
  ],
  "totalValue": 1250.45
}
```

### Wallet Activity

Get recent transaction activity for a wallet.

**Endpoint:** `GET /wallet/:address/activity`

## WebSocket API

### Connection

Connect to our WebSocket server for real-time updates:

**WebSocket URL:** `wss://clawswaps.onrender.com`

**JavaScript Example:**

```javascript
const ws = new WebSocket('wss://clawswaps.onrender.com');

ws.onopen = () => {
  console.log('Connected to Clawap Exchange');

  // Subscribe to price feed
  ws.send(JSON.stringify({
    type: 'subscribe',
    channel: 'prices',
    tokens: ['SOL', 'USDC']
  }));
};

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Price update:', data);
};
```

### Price Feed

Subscribe to real-time price updates for tokens.

**Subscribe Message:**

```json
{
  "type": "subscribe",
  "channel": "prices",
  "tokens": ["SOL", "USDC", "BONK"]
}
```

**Price Update Message:**

```json
{
  "type": "price_update",
  "data": {
    "token": "SOL",
    "price": 156.04,
    "change24h": 2.5,
    "volume24h": 15000000,
    "timestamp": 1699564800000
  }
}
```

### Orderbook Stream

Subscribe to orderbook updates for a trading pair.

**Subscribe Message:**

```json
{
  "type": "subscribe",
  "channel": "orderbook",
  "pair": "SOL/USDC"
}
```

## Resources

- **Website:** [https://clawswaps.xyz](https://clawswaps.xyz)
- **GitHub:** [https://github.com/clawswaps/clawswaps-api](https://github.com/clawswaps/clawswaps-api)
- **Twitter:** [https://x.com/clawdswaps](https://x.com/clawdswaps)

## Support

For support, questions, or feature requests:

- Open an issue on [GitHub](https://github.com/clawswaps/clawswaps-api)
- Follow us on [Twitter](https://x.com/clawdswaps)
- Visit our website at [clawswaps.xyz](https://clawswaps.xyz)
