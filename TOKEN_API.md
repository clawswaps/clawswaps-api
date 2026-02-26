# Token API

The Token API provides access to token information, pricing, and metadata on the Solana blockchain.

## Endpoints

### GET /tokens

Get a list of all supported tokens with metadata.

#### Request

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| verified | boolean | No | Filter for verified tokens only |
| limit | number | No | Number of results (default: 100, max: 500) |
| offset | number | No | Pagination offset (default: 0) |
| search | string | No | Search by token name or symbol |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/tokens?verified=true&limit=50"
```

#### Response

```json
{
  "success": true,
  "tokens": [
    {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "name": "Solana",
      "decimals": 9,
      "logoURI": "https://raw.githubusercontent.com/solana-labs/token-list/main/assets/mainnet/So11111111111111111111111111111111111111112/logo.png",
      "verified": true,
      "volume24h": 15000000,
      "price": 156.04,
      "change24h": 2.5,
      "marketCap": 65000000000
    },
    {
      "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
      "symbol": "USDC",
      "name": "USD Coin",
      "decimals": 6,
      "logoURI": "https://raw.githubusercontent.com/solana-labs/token-list/main/assets/mainnet/EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v/logo.png",
      "verified": true,
      "volume24h": 25000000,
      "price": 1.00,
      "change24h": 0.01,
      "marketCap": 32000000000
    }
  ],
  "count": 150,
  "limit": 50,
  "offset": 0
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| tokens | array | Array of token objects |
| tokens[].mint | string | Token mint address |
| tokens[].symbol | string | Token symbol |
| tokens[].name | string | Token name |
| tokens[].decimals | number | Number of decimal places |
| tokens[].logoURI | string | URL to token logo image |
| tokens[].verified | boolean | Whether the token is verified |
| tokens[].volume24h | number | 24-hour trading volume in USD |
| tokens[].price | number | Current price in USD |
| tokens[].change24h | number | 24-hour price change percentage |
| tokens[].marketCap | number | Market capitalization in USD |
| count | number | Total number of tokens matching filters |
| limit | number | Number of results returned |
| offset | number | Pagination offset |

---

### GET /tokens/:mint

Get detailed information about a specific token.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| mint | string | Yes | Token mint address |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/tokens/So11111111111111111111111111111111111111112"
```

#### Response

```json
{
  "success": true,
  "token": {
    "mint": "So11111111111111111111111111111111111111112",
    "symbol": "SOL",
    "name": "Solana",
    "decimals": 9,
    "logoURI": "https://raw.githubusercontent.com/solana-labs/token-list/main/assets/mainnet/So11111111111111111111111111111111111111112/logo.png",
    "verified": true,
    "description": "Solana is a high-performance blockchain supporting builders around the world creating crypto apps that scale.",
    "website": "https://solana.com",
    "twitter": "https://twitter.com/solana",
    "coingeckoId": "solana",
    "price": 156.04,
    "change24h": 2.5,
    "change7d": 8.3,
    "volume24h": 15000000,
    "marketCap": 65000000000,
    "totalSupply": 583000000,
    "circulatingSupply": 416000000,
    "holders": 1250000
  }
}
```

---

### GET /price

Get real-time prices for one or multiple tokens.

#### Request

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| ids | string | Yes | Comma-separated list of token mint addresses |
| currency | string | No | Currency for price (default: usd) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/price?ids=So11111111111111111111111111111111111111112,EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"
```

#### Response

```json
{
  "success": true,
  "data": {
    "So11111111111111111111111111111111111111112": {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "price": 156.04,
      "change24h": 2.5,
      "volume24h": 15000000,
      "timestamp": 1699564800000
    },
    "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v": {
      "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
      "symbol": "USDC",
      "price": 1.00,
      "change24h": 0.01,
      "volume24h": 25000000,
      "timestamp": 1699564800000
    }
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| data | object | Object with mint addresses as keys |
| data[mint].mint | string | Token mint address |
| data[mint].symbol | string | Token symbol |
| data[mint].price | number | Current price in specified currency |
| data[mint].change24h | number | 24-hour price change percentage |
| data[mint].volume24h | number | 24-hour trading volume |
| data[mint].timestamp | number | Unix timestamp of price data |

---

### GET /tokens/trending

Get trending tokens based on volume and price action.

#### Request

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| limit | number | No | Number of results (default: 10, max: 50) |
| timeframe | string | No | Time period: 1h, 24h, 7d (default: 24h) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/tokens/trending?limit=10&timeframe=24h"
```

#### Response

```json
{
  "success": true,
  "trending": [
    {
      "mint": "DezXAZ8z7PnrnRJjz3wXBoRgixCa6xjnB7YaB1pPB263",
      "symbol": "BONK",
      "name": "Bonk",
      "decimals": 5,
      "logoURI": "https://...",
      "price": 0.00001234,
      "change24h": 45.6,
      "volume24h": 8500000,
      "volumeChange24h": 230.5,
      "marketCap": 850000000,
      "rank": 1
    }
  ],
  "timeframe": "24h",
  "limit": 10
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| trending | array | Array of trending token objects |
| trending[].mint | string | Token mint address |
| trending[].symbol | string | Token symbol |
| trending[].name | string | Token name |
| trending[].price | number | Current price |
| trending[].change24h | number | 24-hour price change percentage |
| trending[].volume24h | number | 24-hour trading volume |
| trending[].volumeChange24h | number | Volume change percentage |
| trending[].rank | number | Trending rank position |
| timeframe | string | Time period used |
| limit | number | Number of results returned |

---

### GET /tokens/search

Search for tokens by name or symbol.

#### Request

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| q | string | Yes | Search query (minimum 2 characters) |
| limit | number | No | Number of results (default: 20, max: 100) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/tokens/search?q=sol&limit=10"
```

#### Response

```json
{
  "success": true,
  "results": [
    {
      "mint": "So11111111111111111111111111111111111111112",
      "symbol": "SOL",
      "name": "Solana",
      "decimals": 9,
      "logoURI": "https://...",
      "verified": true,
      "price": 156.04
    }
  ],
  "query": "sol",
  "count": 1
}
```

## Error Responses

All endpoints may return error responses in the following format:

```json
{
  "success": false,
  "error": {
    "code": "TOKEN_NOT_FOUND",
    "message": "Token with mint address not found"
  }
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| TOKEN_NOT_FOUND | Token mint address not found |
| INVALID_MINT | Invalid token mint address format |
| INVALID_QUERY | Invalid or missing search query |
| INVALID_TIMEFRAME | Invalid timeframe parameter |
| NO_PRICE_DATA | Price data not available for token |

## Best Practices

1. Cache token metadata to reduce API calls
2. Use bulk price requests when fetching multiple token prices
3. Implement proper error handling for missing tokens
4. Validate mint addresses before making requests
5. Use search endpoint for user-facing token selection
6. Subscribe to WebSocket price feeds for real-time updates

## Rate Limits

Token API endpoints are subject to the following rate limits:

- Free tier: 60 requests per minute
- Pro tier: 600 requests per minute
- Enterprise: Unlimited

## Resources

- **Website:** [https://clawswaps.xyz](https://clawswaps.xyz)
- **GitHub:** [https://github.com/clawswaps/clawswaps-api](https://github.com/clawswaps/clawswaps-api)
- **Twitter:** [https://x.com/clawdswaps](https://x.com/clawdswaps)
