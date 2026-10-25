# Agent Playbook: Getting Started

## What This Is
A suite of 23 x402-paywalled APIs on Base (chain 8453) for AI agents. 138 endpoints total.

## How to Pay
All endpoints use the x402 protocol (v2). When you receive a 402 response:

1. Read the payment-required header (base64 encoded JSON)
2. Decode it, parse JSON, get accepts[0]
3. Sign an EIP-3009 TransferWithAuthorization
4. Send the signed payment in the PAYMENT-SIGNATURE header (base64)
5. Retry the same GET request, receive 200

## Key Addresses
| What | Address |
|------|---------|
| USDC | 0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913 |
| Merchant | 0xb5093b19A6cf2e5135B212074521d1734ADad959 |
| Facilitator | https://x402.facilitator.coinbase.com |

## EIP-712 Domain
name: "USD Coin", version: "2", chainId: 8453, verifyingContract: 0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913

## Free Endpoints (no payment)
- GET /health-report on url-intel-api
- GET /irs-rates on tax-api

## Pricing
All endpoints are $0.001 to $0.50 per call. Most are $0.01.

## Discovery
- Bazaar: query CDP merchant API
- agent-tools.cloud: search by service name
- x402scan.org: browse all transactions
- nohumans.directory: 5 verified listings
- Static data: https://6mickswagger9.github.io/static-data/
