# Source provenance

The P2P overview was imported unchanged from the owner's `Raast_P2P_Module_1_Training_Package.zip`. The P2P Remittance overview in `modules/02-p2p-remittance/` adapts that Module 1 document and aligns it with the supplied home-remittance specifications. Discovery and payment messages stay the same. A remittance is transaction type 005 and carries the extra origin and conversion details used for the beneficiary credit and e-PRC. The overview does not reproduce API fields or message schemas.

The Alias Management overview in `modules/08-alias-management/` follows the supplied two-bank walkthrough of how a Raast ID is registered and moved. It keeps the customer story and the CAS steps a new joiner needs. It does not reproduce API paths, hosts, credentials, or test-case data.

The P2M Issuing overview in `modules/03-p2m-issuing/` follows the supplied Open Connect direct-posting specifications for the customer's bank, with the four flows kept apart. Pay by alias starts with an alias inquiry. Static QR does not. Both wait for `pacs.002/AUTH`. Dynamic QR and a request to pay are checked against the open request, and the merchant side receives `pacs.008` as an advice. It does not reproduce API fields or QR tag maps.

The P2M Acquiring overview in `modules/04-p2m-acquiring/` follows the supplied Open Connect acquiring specifications. The sequence diagrams name the Merchant Acquiring System and Middleware. A merchant alias is only for pay by alias. Every received payment checks the DBA name and IBAN, then credits this bank or sends the credit to the bank that holds the account. It does not reproduce API fields.

The Bulk Sending overview in `modules/05-bulk-payments/` follows the supplied Open Connect bulk-sending specifications. The bank channel calls Paysys, Paysys posts the batch to Raast, and later replies cover failed pre-validation accounts, a return payment, and the end-to-end status. The overview does not reproduce API fields, internal services, or queries. Bulk receiving is a placeholder.

The Title Fetch V2 overview in `modules/06-title-fetch-2/` expands the Module 1 discovery path for a destination bank plus a legacy account number. The payee bank returns the IBAN and the account title, and the payment stays in Module 1. It does not add fields or message schemas beyond that overview. Title Fetch 2.0 Bulk remains a placeholder.

The remaining modules are planning placeholders. This publication does not independently certify current Raast protocol specifications.

Assessment files are maintained in a private repository and are not part of this public repository's source or history.
