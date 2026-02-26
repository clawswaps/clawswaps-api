# Wallet API

The Wallet API provides access to wallet information, balances, and transaction history on the Solana blockchain.

## Endpoints

### GET /wallet/:address/balance

Get total portfolio balance in USD for a wallet.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| address | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| currency | string | No | Currency for balance (default: usd) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/wallet/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU/balance"
```

#### Response

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "balance": {
    "total": 1250.45,
    "sol": 5.23,
    "tokens": 434.22,
    "currency": "usd"
  },
  "timestamp": 1699564800000
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| wallet | string | Wallet address |
| balance.total | number | Total portfolio value |
| balance.sol | number | SOL balance value |
| balance.tokens | number | SPL tokens total value |
| balance.currency | string | Currency used for values |
| timestamp | number | Unix timestamp of data |

---

### GET /wallet/:address/tokens

List all tokens held by a wallet with balances and values.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| address | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| includeZeroBalance | boolean | No | Include tokens with zero balance (default: false) |
| verifiedOnly | boolean | No | Only show verified tokens (default: false) |
| currency | string | No | Currency for values (default: usd) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/wallet/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU/tokens?verifiedOnly=true"
```

#### Response

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "tokens": [
    {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "name": "Solana",
      "balance": 5.23,
      "decimals": 9,
      "usdValue": 816.23,
      "price": 156.04,
      "change24h": 2.5,
      "logoURI": "https://...",
      "verified": true
    },
    {
      "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
      "symbol": "USDC",
      "name": "USD Coin",
      "balance": 434.22,
      "decimals": 6,
      "usdValue": 434.22,
      "price": 1.00,
      "change24h": 0.01,
      "logoURI": "https://...",
      "verified": true
    }
  ],
  "totalValue": 1250.45,
  "currency": "usd",
  "timestamp": 1699564800000
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| wallet | string | Wallet address |
| tokens | array | Array of token holdings |
| tokens[].mint | string | Token mint address |
| tokens[].symbol | string | Token symbol |
| tokens[].name | string | Token name |
| tokens[].balance | number | Token balance (decimal adjusted) |
| tokens[].decimals | number | Token decimals |
| tokens[].usdValue | number | USD value of holdings |
| tokens[].price | number | Current token price |
| tokens[].change24h | number | 24-hour price change percentage |
| tokens[].verified | boolean | Whether token is verified |
| totalValue | number | Total portfolio value |
| currency | string | Currency used for values |
| timestamp | number | Unix timestamp of data |

---

### GET /wallet/:address/activity

Get recent transaction activity for a wallet.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| address | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| limit | number | No | Number of transactions (default: 50, max: 100) |
| offset | number | No | Pagination offset (default: 0) |
| type | string | No | Filter by type: all, swap, transfer, mint (default: all) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/wallet/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU/activity?limit=10&type=swap"
```

#### Response

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "activities": [
    {
      "signature": "5j7s6NiJS3JAkvgkoc18WVAsiSaci2pxB2A6ueCJP4tprA2TFg9wSyTLeYouxPBJEMzJinENTkpA52YStRW5Dia7",
      "type": "swap",
      "timestamp": 1699564800,
      "status": "confirmed",
      "details": {
        "inputToken": {
          "mint": "So11111111111111111111111111111111111111112",
          "symbol": "SOL",
          "amount": 1.0
        },
        "outputToken": {
          "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
          "symbol": "USDC",
          "amount": 156.04
        }
      },
      "fee": 0.000005,
      "blockTime": 1699564800
    },
    {
      "signature": "3mZ7Y2VxKjH5PnJc4WVAsiSaci2pxB2A6ueCJP4tprA2TFg9wSyTLeYouxPBJEMzJinENTkpA52YStRW5Dia8",
      "type": "transfer",
      "timestamp": 1699564700,
      "status": "confirmed",
      "details": {
        "token": {
          "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
          "symbol": "USDC",
          "amount": 100.0
        },
        "from": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
        "to": "9yKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsV",
        "direction": "out"
      },
      "fee": 0.000005,
      "blockTime": 1699564700
    }
  ],
  "total": 42,
  "limit": 10,
  "offset": 0
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| wallet | string | Wallet address |
| activities | array | Array of transaction activities |
| activities[].signature | string | Transaction signature |
| activities[].type | string | Transaction type (swap, transfer, mint) |
| activities[].timestamp | number | Unix timestamp |
| activities[].status | string | Transaction status |
| activities[].details | object | Type-specific transaction details |
| activities[].fee | number | Transaction fee in SOL |
| activities[].blockTime | number | Block timestamp |
| total | number | Total number of activities |
| limit | number | Number of results returned |
| offset | number | Pagination offset |

---

### GET /wallet/:address/portfolio

Get portfolio performance and analytics for a wallet.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| address | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| timeframe | string | No | Time period: 24h, 7d, 30d, 1y (default: 7d) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/wallet/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU/portfolio?timeframe=7d"
```

#### Response

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "portfolio": {
    "currentValue": 1250.45,
    "change": 125.30,
    "changePercent": 11.15,
    "timeframe": "7d",
    "highValue": 1280.00,
    "lowValue": 980.00,
    "startValue": 1125.15
  },
  "topHoldings": [
    {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "value": 816.23,
      "percentage": 65.3
    },
    {
      "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
      "symbol": "USDC",
      "value": 434.22,
      "percentage": 34.7
    }
  ],
  "performance": {
    "totalSwaps": 15,
    "totalVolume": 5420.00,
    "avgSwapSize": 361.33,
    "profitLoss": 85.50
  }
}
```

---

### GET /wallet/:address/nfts

Get NFTs owned by a wallet.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| address | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| limit | number | No | Number of results (default: 50, max: 200) |
| offset | number | No | Pagination offset (default: 0) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/wallet/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU/nfts?limit=20"
```

#### Response

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "nfts": [
    {
      "mint": "8xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsV",
      "name": "Degen Ape #1234",
      "symbol": "DAPE",
      "image": "https://...",
      "collection": "Degen Ape Academy",
      "attributes": [],
      "floorPrice": 2.5,
      "lastSale": 3.2
    }
  ],
  "count": 8,
  "limit": 20,
  "offset": 0
}
```

## Error Responses

All endpoints may return error responses in the following format:

```json
{
  "success": false,
  "error": {
    "code": "INVALID_WALLET",
    "message": "Invalid wallet address format"
  }
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| INVALID_WALLET | Invalid wallet address format |
| WALLET_NOT_FOUND | Wallet address not found or has no activity |
| INVALID_TIMEFRAME | Invalid timeframe parameter |
| RATE_LIMIT_EXCEEDED | API rate limit exceeded |

## Best Practices

1. Cache wallet data appropriately to reduce API calls
2. Use pagination for large result sets
3. Implement proper error handling for invalid addresses
4. Validate wallet addresses before making requests
5. Use filters to reduce response size
6. Subscribe to WebSocket for real-time balance updates

## Rate Limits

Wallet API endpoints are subject to the following rate limits:

- Free tier: 60 requests per minute
- Pro tier: 600 requests per minute
- Enterprise: Unlimited

## Resources

- **Website:** [https://clawswaps.xyz](https://clawswaps.xyz)
- **GitHub:** [https://github.com/clawswaps/clawswaps-api](https://github.com/clawswaps/clawswaps-api)
- **Twitter:** [https://x.com/clawswaps](https://x.com/clawswaps)
