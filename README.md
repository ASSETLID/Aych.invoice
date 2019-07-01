# liquidity-invoice-generation
Invoice generation system compatible with Liquidity Network

**IMPORTANT** Requires `web3-utils` and `bignumber.js` dependencies

### Usage

```typescript
const invoice: Invoice = createInvoice({
  network: 4, // Network id
  publicKey: '0xE6987CD613Dfda0995A95b3E6acBAbECecd41376', // public key of recipient
  // Generate unique id for invoice, payment by this invoice can be tracked by nonce which is derived by
  // invoice's id and amount. If id is not generated, then the invoice can be used multiple times.
  generateId: true,
  // Address of the operator, if not defined, on-chain invoice will be generated.
  operatorAddress: '0x4FED1fC4144c223aE3C1553be203cDFcbD38C581',
  tokenAddress: '0x4FED1fC4144c223aE3C1553be203cDFcbD38C581', // Token address, if not defined, operator address applied.
  amount: '12000000000000', // Optional. BigNumber or number also can be used.
})

const encodedInvoice: string = encodeInvoice(invoice) // Used to generate QR code

const decodedInvoice: Invoice = decodeInvoice(encodedInvoice) // Decode invoice after QR code scanning

// Note: Here, decodedInvoice equals invoice
```

- Feed `encodedInvoice` into a QR code generator as plain text. (i.g: user https://www.the-qrcode-generator.com or a library) to be scanned by the user.
- Feed `decodedInvoice` into the `sendTransfer` function of the NOCUSManager. 
- If no amount is specified invoice.amount will be undefined, it needs to be set before making a transfer with the nocust manager client side.
- If tokenAddress is not specified, Ether will be used. 

The typical merchant needs to track the completion of payments by its customer, to do so we use `generateId`.

1. SERVER SIDE, generate an invoice with `generateId` set to `true` and save the nonce in the invoice object `invoice.nonce` in the merchant server db. It is important to be done server side otherwise the client can manipulate the value. 
2. Encode the invoice with `encodeInvoice` function and send it to the client front-end to display the QR code
3. Wait for an incoming payment with the nocust-manager that has the correct nonce value AND amount.