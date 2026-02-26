# Swap API

The Swap API allows you to get quotes and execute token swaps on the Solana blockchain.

## Endpoints

### GET /swap/quote

Get the best swap quote for a token pair with optimal routing.

#### Request

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| inputMint | string | Yes | Input token mint address |
| outputMint | string | Yes | Output token mint address |
| amount | number | Yes | Amount in smallest unit (lamports for SOL, smallest unit for SPL tokens) |
| slippageBps | number | No | Slippage tolerance in basis points (default: 50, which is 0.5%) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/swap/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v&amount=1000000000&slippageBps=50"
```

#### Response

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

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| quote | object | The quote information |
| quote.inputMint | string | Input token mint address |
| quote.outputMint | string | Output token mint address |
| quote.inAmount | string | Input amount in smallest unit |
| quote.outAmount | string | Expected output amount in smallest unit |
| quote.priceImpactPct | number | Price impact percentage |
| quote.marketInfos | array | Array of market route information |

---

### POST /swap/execute

Execute a swap transaction based on a quote.

#### Request

**Headers:**
```
Content-Type: application/json
```

**Body:**

```json
{
  "quoteResponse": { ... },
  "userPublicKey": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "wrapAndUnwrapSol": true
}
```

**Body Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| quoteResponse | object | Yes | The quote object returned from /swap/quote |
| userPublicKey | string | Yes | The user's Solana wallet public key |
| wrapAndUnwrapSol | boolean | No | Automatically wrap/unwrap SOL (default: true) |

#### Example Request

```bash
curl -X POST "https://clawswaps.onrender.com/api/swap/execute" \
  -H "Content-Type: application/json" \
  -d '{
    "quoteResponse": {...},
    "userPublicKey": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
    "wrapAndUnwrapSol": true
  }'
```

#### Response

```json
{
  "success": true,
  "swapTransaction": "4hXTCkRzt9WyecN...",
  "lastValidBlockHeight": 150000000
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| success | boolean | Indicates if the request was successful |
| swapTransaction | string | Base64 encoded serialized transaction |
| lastValidBlockHeight | number | Block height at which the transaction expires |

#### Next Steps

After receiving the swap transaction:

1. Deserialize the transaction
2. Sign the transaction with the user's wallet
3. Send the signed transaction to the Solana network
4. Wait for confirmation

---

### GET /swap/history/:wallet

Retrieve swap transaction history for a wallet address.

#### Request

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| wallet | string | Yes | Solana wallet address |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| limit | number | No | Number of results to return (default: 50, max: 100) |
| offset | number | No | Pagination offset (default: 0) |

#### Example Request

```bash
curl "https://clawswaps.onrender.com/api/swap/history/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU?limit=10&offset=0"
```

#### Response

```json
{
  "success": true,
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "history": [
    {
      "signature": "5j7s6NiJS3JAkvgkoc18WVAsiSaci2pxB2A6ueCJP4tprA2TFg9wSyTLeYouxPBJEMzJinENTkpA52YStRW5Dia7",
      "timestamp": 1699564800,
      "inputMint": "So11111111111111111111111111111111111111112",
      "outputMint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
      "inAmount": "1000000000",
      "outAmount": "156040000",
      "status": "confirmed"
    }
  ],
  "total": 42,
  "limit": 10,
  "offset": 0
}
```

## Error Responses

All endpoints may return error responses in the following format:

```json
{
  "success": false,
  "error": {
    "code": "INVALID_AMOUNT",
    "message": "Amount must be greater than 0"
  }
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| INVALID_MINT | Invalid token mint address |
| INVALID_AMOUNT | Invalid or missing amount parameter |
| INSUFFICIENT_LIQUIDITY | Not enough liquidity for the requested swap |
| SLIPPAGE_EXCEEDED | Price moved beyond slippage tolerance |
| QUOTE_EXPIRED | Quote has expired, request a new one |
| INVALID_WALLET | Invalid wallet address format |

## Best Practices

1. Always validate user input before making API calls
2. Handle slippage appropriately for different market conditions
3. Implement proper error handling for all API responses
4. Cache quotes briefly but refresh before execution
5. Monitor transaction status after sending to the network
6. Implement retry logic for network failures

## Rate Limits

Swap API endpoints are subject to the following rate limits:

- Free tier: 60 requests per minute
- Pro tier: 600 requests per minute
- Enterprise: Unlimited

## Resources

- **Website:** [https://clawswaps.xyz](https://clawswaps.xyz)
- **GitHub:** [https://github.com/clawswaps/clawswaps-api](https://github.com/clawswaps/clawswaps-api)
- **Twitter:** [https://x.com/clawswaps](https://x.com/clawswaps)
