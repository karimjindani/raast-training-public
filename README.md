# Raast Technical Training

Technical training material for new engineers learning Raast, beginning with P2P payments. The intended audience includes engineering, DevOps, application reliability, and operations teams.

This public library contains the P2P overview and a structure for future modules. Quiz source files and the answer key are held in a separate private maintainer repository.

## Start with P2P

Read the [P2P Payment Overview](modules/01-p2p/01_Raast_P2P_Payment_Overview.md). Its content is preserved unchanged from the supplied training package. GitHub displays the embedded Mermaid sequence diagrams.

The interactive assessment is hosted separately. Ask your instructor for access; quiz source files and the answer key are not included here.

## Curriculum

| Module | Material status |
| --- | --- |
| [P2P Payments](modules/01-p2p/README.md) | Overview available; assessment hosted separately |
| [P2P Remittance](modules/02-p2p-remittance/README.md) | Planned |
| [P2M Issuing](modules/03-p2m-issuing/README.md) | Planned |
| [P2M Acquiring](modules/04-p2m-acquiring/README.md) | Planned |
| [Bulk Payments](modules/05-bulk-payments/README.md) | Planned |
| [Title Fetch 2.0](modules/06-title-fetch-2/README.md) | Planned |
| [Title Fetch 2.0 Bulk](modules/07-title-fetch-2-bulk/README.md) | Planned |
| [Alias Management](modules/08-alias-management/README.md) | Planned |
| [Limit Management](modules/09-limit-management/README.md) | Planned |
| [PISP](modules/10-pisp/README.md) | Planned |
| [PayPak POS](modules/11-paypak-pos/README.md) | Planned |
| [Liquidity and RTGS Interaction](modules/12-liquidity-rtgs/README.md) | Planned; detailed specifications pending |

## Adding a module

The current P2P module is an introductory overview. The future curriculum folders reserve space for validated lessons; their presence does not imply that those lessons are complete. See [source provenance](SOURCE.md) for the imported package and integrity hashes.

Keep each module in its own numbered folder, with a README linking to its overview and the learner assessment when available. Keep assessment source files and answer keys in the private maintainer repository. Preserve original source filenames when importing an existing validated package. Embed sequence diagrams in fenced `mermaid` blocks in the overview.

Add technical details only when supported by specifications supplied and validated by the material owner. Keep incomplete topics explicitly marked as pending. Do not infer message identifiers, field mappings, timeout values, status transitions, retry rules, or settlement behavior for future modules.

When updating an existing lesson, record its source and validation status and keep the overview, quiz, and answer key consistent. Planned modules are curriculum placeholders, not technical specifications.
