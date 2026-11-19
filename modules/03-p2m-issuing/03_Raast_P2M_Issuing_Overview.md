# Module 3 — Raast P2M Issuing: An Overview

Read [Module 1 — Raast P2P Payment](../01-p2p/01_Raast_P2P_Payment_Overview.md) first. That module is one person paying another person. This module is the Customer Service Provider: the customer's bank paying a merchant.

The customer's bank places the hold and later confirms the debit. Paysys posts the payment to Raast. The merchant's bank is covered in [P2M Acquiring](../04-p2m-acquiring/04_Raast_P2M_Acquiring_Overview.md).

A scanned QR that belongs to a person follows Module 1. A merchant QR stays in this module.

P2M has four flows. The customer bank does not treat them as one payment.

| Flow | How it starts | Alias inquiry | What the customer bank waits for |
|---|---|---|---|
| Pay by Alias | The customer enters the merchant Raast ID | Yes. Raast resolves the merchant before the customer confirms | The merchant bank answers `pacs.008` with `pacs.002/AUTH`. Raast then sends `pacs.002/ACSP` |
| Static QR | The customer scans a merchant QR | No. The QR already carries the merchant | Same as pay by alias: `pacs.002/AUTH`, then `pacs.002/ACSP` |
| Dynamic QR | The merchant has already raised a request to pay, and the customer scans that QR | No | Raast checks that request. It answers the customer bank with `pacs.002/ACSP`. The `pacs.008` the merchant side receives is an advice to credit |
| RTP, now or later | The merchant asks the customer to pay | No merchant alias inquiry | The money moves when the customer pays. Raast checks that payment against the request, the same way as a dynamic QR |

## Learning objectives

By the end of this module, a new technical employee should be able to:

- Name the four P2M flows and say which one uses a merchant alias inquiry.
- Describe a static QR or pay-by-alias payment, from the hold to the merchant bank's `pacs.002/AUTH`.
- Describe a dynamic QR, including why `pacs.008` is an advice on the merchant side.
- Describe a request to pay, including pay now and pay later.
- Explain a rejection, a customer's request for a return, and the inquiry used when the result is missing.

---

## 1. What this module adds

The customer starts the payment, or the merchant asks the customer to pay. Either way, the customer's bank is the issuer. It checks the customer, holds the money, and confirms the debit only after Raast accepts the payment.

Paysys is the Open Connect service between that bank and Raast.

> **Finding the merchant answers: “Who is being paid?”**  
> **The payment answers: “Has Raast accepted this customer's money for that merchant?”**

`pacs.008`, `pacs.002/AUTH`, `pacs.002/ACSP`, `pacs.002/RJCT`, `pacs.004`, and `pacs.028` mean the same thing here as in Module 1. Which of those messages the customer bank sees depends on the flow in the table above.

---

## 2. Pay by Alias

This is the only issuing flow that starts with a merchant alias inquiry. The customer enters the merchant Raast ID. Paysys asks Raast who that merchant is. The customer sees the merchant name, category, bank, and account, then confirms.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast

    C->>B: Enter the merchant Raast ID and amount
    B->>P: Who is this merchant?
    P->>R: Merchant alias inquiry
    R-->>P: Merchant name, bank, and account
    P-->>B: Merchant details
    B-->>C: Show the merchant for confirmation
```

The payment that follows is the same payment as a static QR, in section 3. The merchant bank is expected to answer `pacs.008` with `pacs.002/AUTH`.

---

## 3. Static QR and pay by alias

A static QR already carries the merchant. The customer bank shows the merchant name and category from the QR. The amount is the amount on the QR, or an amount the customer enters. A tip is added when the merchant asked for one. There is no alias inquiry.

Pay by alias reaches this same payment only after the inquiry in section 2.

The customer confirms in the bank's app, using the same approval the bank already uses for a transfer. The bank checks the amount, any tip, and the customer's limits, then asks the core system to hold the funds.

Paysys sends `pacs.008` to Raast. Raast sends it to the merchant's bank. That bank checks the merchant and answers `pacs.002/AUTH`. Raast updates the two banks' positions and sends `pacs.002/ACSP` to both sides.

Paysys marks the payment complete and tells the customer's bank. The bank confirms the debit for the held amount and shows the customer the result.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast
    participant MB as Merchant Bank

    C->>B: Confirm a static QR or an alias payment
    B->>B: Check limits
    B->>B: Place a hold on the customer account
    B->>P: Post this merchant payment
    P->>R: pacs.008
    R->>MB: pacs.008
    MB->>MB: Check the merchant and the account
    MB-->>R: pacs.002 / AUTH
    R->>R: Update both banks' positions
    par Accepted result
        R-->>P: pacs.002 / ACSP
    and
        R-->>MB: pacs.002 / ACSP
    end
    P-->>B: Payment completed
    B->>B: Confirm the debit for the held amount
    B-->>C: Payment successful
```

---

## 4. When a static QR or alias payment is rejected

The hold is placed before Raast sees the payment. If the merchant's bank cannot accept it, that bank answers with a decline. Raast sends `pacs.002/RJCT` to both sides. Paysys marks the payment failed, and the customer's bank releases the hold.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast
    participant MB as Merchant Bank

    Note over B: The hold is already placed
    P->>R: pacs.008
    R->>MB: pacs.008
    MB-->>R: pacs.002 / NAUT
    par Rejected result
        R-->>P: pacs.002 / RJCT
    and
        R-->>MB: pacs.002 / RJCT
    end
    P-->>B: Payment failed
    B->>B: Release the hold
    B-->>C: Payment failed
```

If the core system cannot hold the amount, because the balance is too low or the account is restricted, the bank tells the customer and stops. Paysys is not called. This is true for every flow on this page.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank

    C->>B: Confirm payment to the merchant
    B->>B: Try to place a hold
    B-->>C: Payment failed<br/>The hold was not placed
```

---

## 5. Dynamic QR

A dynamic QR is a request to pay for one bill. The merchant creates that request with Raast before the customer scans. The QR carries the merchant and the request. The customer bank does not inquire on an alias, and Raast does not deliver the request to the customer bank in advance.

The customer scans and confirms. The bank places the hold, and Paysys sends `pacs.008` for that request. Raast checks that the request is still open.

When the request is open, Raast answers the customer bank with `pacs.002/ACSP`. The customer bank confirms the debit. The `pacs.008` the merchant side receives is an advice to credit the merchant. The merchant bank does not answer that advice with `pacs.002/AUTH`.

When the request is not open, Raast answers the customer bank with `pacs.002/RJCT`. The bank releases the hold. There is no decline from the merchant bank on this path.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast
    participant MB as Merchant Bank

    C->>B: Scan the dynamic QR and confirm
    Note over B: The QR already identifies the merchant and the request
    B->>B: Check limits and place a hold
    B->>P: Pay this request
    P->>R: pacs.008 for the request
    R->>R: Check the request is still open

    alt Request is open
        R-->>P: pacs.002 / ACSP
        R-->>MB: pacs.008<br/>Advice to credit the merchant
        P-->>B: Payment completed
        B->>B: Confirm the debit
        B-->>C: Payment successful
    else Request is not open
        R-->>P: pacs.002 / RJCT
        P-->>B: Payment failed
        B->>B: Release the hold
        B-->>C: Payment failed
    end
```

---

## 6. Request to pay, now or later

A merchant can ask the customer to pay without a QR. The merchant looks up the customer's Raast ID, then sends Raast a request to pay. Raast delivers that request to the customer's bank through Paysys. This lookup is the customer's alias. It is not a merchant alias inquiry.

The bank checks that the customer can approve it on a digital channel. If the customer cannot, the bank rejects the request and Raast tells the merchant.

If the customer can approve it, the customer may accept, reject, or block the request.

- **Pay later.** The bank tells Raast that the request was received and the customer will pay before the due date. The merchant is told the same. The money does not move yet.
- **Pay now.** The customer accepts or rejects immediately.

Acceptance, now or later, starts the payment below. It is the dynamic-QR shape: `pacs.008` against the open request, `pacs.002/ACSP` back to the customer bank, and an advice to the merchant side. It is not the `pacs.002/AUTH` payment in section 3.

```mermaid
sequenceDiagram
    autonumber
    participant M as Merchant
    participant R as Raast
    participant P as Paysys
    participant B as Customer Bank
    participant C as Customer

    M->>R: Look up the customer's Raast ID
    R-->>M: Customer account
    M->>R: Request to pay
    R->>P: Request to pay
    P->>B: Request to pay

    alt Customer cannot approve on a digital channel
        B->>P: Reject the request
        P->>R: Request rejected
        R-->>M: Request rejected
    else Pay later
        B->>P: Request received
        P->>R: Customer will pay later
        R-->>M: Request received
        B-->>C: Pay this merchant by the due date
        C->>B: Accept and pay
    else Pay now
        B-->>C: Accept or reject this request
        C->>B: Accept and pay
    end
```

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast
    participant MB as Merchant Bank

    C->>B: Accept and pay
    B->>B: Place a hold
    B->>P: Pay this request
    P->>R: pacs.008 against the request
    R->>R: Check the request is still open
    R-->>P: pacs.002 / ACSP
    R-->>MB: pacs.008<br/>Advice to credit the merchant
    P-->>B: Payment completed
    B->>B: Confirm the debit
    B-->>C: Payment successful
```

If Raast rejects that payment, the customer bank receives `pacs.002/RJCT` and releases the hold, as in the dynamic QR flow.

---

## 7. Asking for the money back

The customer can ask for a return of the full amount or part of it from the list of merchant payments. The bank checks that the request is within the original amount. Paysys sends the request to Raast, and Raast sends it to the merchant. The merchant decides whether to return the money.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast
    participant M as Merchant

    C->>B: Ask for a full or partial return
    B->>B: Check the amount against the original payment
    B->>P: Request a return
    P->>R: Request a return
    R->>M: Request a return
```

When the merchant does return the money, the merchant sends `pacs.004` for the original payment. Raast confirms that return to the merchant with `pacs.002/ACSP` and sends `pacs.004` to the customer's bank. Paysys asks the bank to credit the customer. The credit is posted so the customer receives the returned amount.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast
    participant M as Merchant

    M->>R: pacs.004<br/>Return the payment
    R-->>M: pacs.002 / ACSP
    R-->>P: pacs.004
    P->>B: Credit the customer
    B->>B: Credit the customer account
    B-->>C: Amount returned
```

---

## 8. When the result is missing

If the customer's bank does not receive the result in time, it asks Paysys about the original payment. Paysys answers from the status it already stored. If it has no status, it asks Raast with `pacs.028`.

A successful answer confirms the debit. A failed answer releases the hold. The bank then shows the customer the result.

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant B as Customer Bank
    participant P as Paysys
    participant R as Raast

    B->>P: What happened to this payment?
    alt Paysys already has the status
        P-->>B: Completed or failed
    else Status is not stored
        P->>R: pacs.028
        R-->>P: Authoritative status
        P-->>B: Completed or failed
    end
    alt Payment completed
        B->>B: Confirm the debit
    else Payment failed
        B->>B: Release the hold
    end
    B-->>C: Show the result
```

---

## 9. End-to-end mental model

```mermaid
flowchart LR
    A[Customer pays a merchant] --> B{Which flow?}
    B -->|Pay by Alias| C[Merchant alias inquiry]
    B -->|Static QR| D[Merchant is already on the QR]
    B -->|Dynamic QR| E[Scan the merchant's request]
    B -->|RTP now or later| F[Merchant request arrives]
    C --> G[Hold, then pacs.008]
    D --> G
    G --> H{Merchant bank answers AUTH?}
    H -->|ACSP| I[Confirm the debit]
    H -->|RJCT| J[Release the hold]
    E --> K[Hold, then pacs.008 against the request]
    F --> L{Customer accepts?}
    L -->|Later, then pays| K
    L -->|Pay now| K
    L -->|No| M[Request ends]
    K --> N{Request still open?}
    N -->|ACSP, advice to merchant| I
    N -->|RJCT| J
```

---

## 10. Terms that are specific to this module

Shared message names are defined in Module 1.

| Term | Meaning in this module |
|---|---|
| Customer Service Provider | The customer's bank. It holds the money and confirms the debit |
| Acquiring | The merchant's bank. It checks the merchant and receives the credit |
| Paysys | Open Connect. Posts the customer's payment to Raast and returns the result |
| Direct posting | The customer's bank keeps the debit. Paysys posts the Raast payment |
| Pay by Alias | The customer enters a merchant Raast ID. This flow starts with an alias inquiry, then waits for `pacs.002/AUTH` |
| Static QR | A merchant QR the customer scans. No alias inquiry. `pacs.008` is answered with `pacs.002/AUTH` |
| Dynamic QR | A QR for one request to pay. `pacs.008` on the merchant side is an advice to credit |
| Request to pay | The merchant asks the customer to pay now, or by a later due date. The later payment is checked against that request |

---

## 11. What to remember

1. P2M issuing has four flows: **pay by alias**, **static QR**, **dynamic QR**, and **RTP now or later**.
2. **Pay by alias** is the flow that inquires on the merchant Raast ID. A **static QR** already carries the merchant, so there is no alias inquiry.
3. Static QR and pay by alias send **`pacs.008`** and wait for the merchant bank's **`pacs.002/AUTH`**, then **`pacs.002/ACSP`**. A decline becomes **`pacs.002/RJCT`**, and the bank releases the hold.
4. A **dynamic QR** is a request to pay. Raast checks that request and answers the customer bank with **`pacs.002/ACSP`**. The merchant side receives **`pacs.008` as an advice**, and does not answer it with **`pacs.002/AUTH`**.
5. **RTP now** and **RTP later** use that same request payment once the customer accepts. Pay later only records that the request was received until then.
6. A return the merchant sends is **`pacs.004`**, and the customer's bank credits the account. A missing result is resolved with **`pacs.028`**.
