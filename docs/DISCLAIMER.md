# Disclaimer and limitations

Deep-Re-Audit is an AI instruction set provided for technical review and decision support. It is not a certification, an independent assurance service, or a guarantee that a system is secure, correct, complete, compliant, or ready for production.

## AI and evidence limitations

An AI agent can miss defects, invent unsupported relationships, misunderstand requirements, or misinterpret tools and test results. Findings must be evaluated against the actual source material and relevant execution evidence. Repeated passes or agreeing agents do not automatically establish independence or truth.

Results apply only to the stated target, version, configuration, environment, reviewed scope, and available evidence. A document review does not prove implementation behavior. A test environment does not automatically prove production behavior. Missing observations do not prove that a failure cannot occur.

The repository's protocol checks and documentation are not empirical proof of audit effectiveness. No detection rate, completeness rate, cross-model equivalence, or production certification is claimed.

## Authorization and operational safety

Use the skill only on systems and information you are authorized to access. Its default is read-only. Any modification, intrusive verification, production command, external transmission, or other side effect remains subject to user authorization and the host's permission rules. Audit findings and dependency graphs never create execution authority.

Before consequential changes, use suitable backups or recovery arrangements, focused tests, dependency review, and an appropriate rollback or forward-repair plan. Additional complexity can itself create defects; a larger audit or graph is not necessarily a better result.

## Privacy and confidentiality

The instruction payload contains no telemetry or payment implementation. That does not make the surrounding AI service private or offline. Data handling depends on the model provider, host, tools, connected services, and your configuration. Review their policies and permissions before supplying confidential information. Keep credentials, personal data, proprietary material, and live audit evidence out of this public repository.

## Independence and attribution

Created by khotjen (khotjen@gmail.com), developed with assistance from OpenAI GPT.

This attribution does not identify the model that performs a future audit. It does not imply endorsement, sponsorship, certification, or a partnership with OpenAI, Anthropic, PayPal, or another provider. Different models and hosts may interpret the same instructions differently.

## Voluntary donations

Donations via PayPal to **khotjen@gmail.com** are optional. They are not a condition of use, proof of audit quality, a purchase of professional assurance, or a guarantee of support. This repository does not verify the payment account, open payment flows, collect payment credentials, or send money.

## Licensing

No standalone reuse license is included in this initial publication. Contact khotjen at **khotjen@gmail.com** about licensing and reuse permissions.

Use relevant tests and qualified review for decisions whose failure could cause material harm. The user and system owner remain responsible for deciding whether the evidence is sufficient for their particular deployment.
