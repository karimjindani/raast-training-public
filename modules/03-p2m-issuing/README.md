# P2M Issuing

Read the [P2M Issuing Overview](03_Raast_P2M_Issuing_Overview.md), including embedded Mermaid sequence diagrams.

This is the Customer Service Provider: the customer's bank paying a merchant. P2M has four flows.

- **Pay by Alias.** The customer enters a merchant Raast ID. The bank inquires on that alias, then sends `pacs.008` and waits for `pacs.002/AUTH`.
- **Static QR.** The QR already carries the merchant. There is no alias inquiry. `pacs.008` is answered with `pacs.002/AUTH`.
- **Dynamic QR.** The merchant has raised a request to pay. `pacs.008` on the merchant side is an advice to credit.
- **RTP, now or later.** The merchant asks the customer to pay. Once the customer accepts, the payment is checked against that request, the same way as a dynamic QR.

The customer's bank keeps the debit, and Paysys posts the payment to Raast.

No separate interactive assessment is published for this module yet. Ask your instructor if an assessment is assigned. Quiz source files and answer keys are not part of this public repository.
