# P2M Acquiring

Read the [P2M Acquiring Overview](04_Raast_P2M_Acquiring_Overview.md), including embedded Mermaid sequence diagrams.

Read [P2M Issuing](../03-p2m-issuing/03_Raast_P2M_Issuing_Overview.md) first. Issuing is the customer's bank. Acquiring is the Merchant Acquiring System, with Middleware between that system and Raast.

A merchant alias is required only for pay by alias. Static QR, dynamic QR, and a request to pay do not need one. Every received payment is checked as a DBA name and IBAN pair, then credited at this bank or sent to the other bank that holds the account.

No separate interactive assessment is published for this module yet. Ask your instructor if an assessment is assigned. Quiz source files and answer keys are not part of this public repository.
