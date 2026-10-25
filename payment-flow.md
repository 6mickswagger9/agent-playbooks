# Agent Playbook: Payment Flow (x402 v2)

## Step-by-Step

### 1. GET the endpoint
Receive 402 Payment Required with payment-required header.

### 2. Decode the header
const challenge = JSON.parse(Buffer.from(headerVal, "base64").toString("utf8"));

### 3. Extract payment requirements
const pay = challenge.accepts[0];
// pay.scheme = "exact"
// pay.network = "eip155:8453"
// pay.amount = "10000" (atomic units, 10000 = $0.01)
// pay.asset = "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913"
// pay.payTo = "0xb5093b19A6cf2e5135B212074521d1734ADad959"

### 4. Sign EIP-3009 TransferWithAuthorization
const authorization = {
  from: wallet.address,
  to: pay.payTo,
  value: pay.amount,
  validAfter: "0",
  validBefore: String(Math.floor(Date.now() / 1000) + 300),
  nonce: ethers.hexlify(ethers.randomBytes(32)),
};

const signature = await wallet.signTypedData(
  { name: "USD Coin", version: "2", chainId: 8453, verifyingContract: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913" },
  { TransferWithAuthorization: [
    { name: "from", type: "address" },
    { name: "to", type: "address" },
    { name: "value", type: "uint256" },
    { name: "validAfter", type: "uint256" },
    { name: "validBefore", type: "uint256" },
    { name: "nonce", type: "bytes32" },
  ]},
  authorization
);

### 5. Build payment payload
const paymentPayload = {
  x402Version: 2,
  resource: challenge.resource,
  accepted: pay,
  payload: {
    signature,
    authorization: { from: wallet.address, to: pay.payTo, value: pay.amount, validAfter: "0", validBefore: authorization.validBefore, nonce: authorization.nonce },
  },
};

### 6. Send payment
const header = Buffer.from(JSON.stringify(paymentPayload)).toString("base64");
const response = await fetch(url, { method: "GET", headers: { "PAYMENT-SIGNATURE": header } });
// response.status === 200 means success

## Common Mistakes
| Mistake | Fix |
|---------|-----|
| EIP-712 domain name "USDC" | Must be "USD Coin" |
| nonce = zero | Must be random 32 bytes |
| validAfter = timestamp | Must be "0" |
| Header name "X-PAYMENT" | Must be "PAYMENT-SIGNATURE" |
| Method PUT | Must be GET |
| ethers _signTypedData | Use signTypedData (v6) |
