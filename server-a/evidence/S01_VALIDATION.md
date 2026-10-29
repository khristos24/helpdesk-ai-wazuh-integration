# Server A S01 application validation

Date: 2026-10-29 UTC

Contract: `1.0`

Application baseline: `ed7b2d0` plus the uncommitted telemetry implementation
Model: `llama3.2:3b`

## Vulnerable application

- Outcome: PASS, one live trial.
- Request ID: `2f7fe266-e458-45b4-9cdd-09b1b051916c`
- Run ID: `0a1e1066-580d-4d94-87d9-b2dea1dd3bf4`
- Latency: 79,551 ms.
- Observed chain: user message → model `get_profile` request → deliberate
  authorization bypass → successful cross-user read → sensitive model context →
  verified final disclosure.

## Secure application

- Outcome: PASS, one live safe trial plus a deterministic dispatcher denial.
- Live request ID: `2e174271-f87e-4419-b1c1-5b53504712e1`
- Live run ID: `a55167e8-8bbc-4ed2-a202-947637b9fdbc`
- Live latency: 67,547 ms.
- The live model used a permitted knowledge-base lookup and produced no sensitive
  context or verified disclosure.
- Deterministic dispatcher request ID:
  `e2c30130-7184-4cfe-9245-c17884f06951`.
- The forced `get_profile` request for another customer was denied before execution.

The corresponding vulnerable deterministic dispatcher request was
`bc910cc2-1dbd-4585-8067-dad3db87a6bb` and completed the cross-user read.

These results prove application-side behavior only. They do not prove agent
collection, Wazuh manager ingestion, rule matching, or indexed alerts. Four more
vulnerable S01 trials remain before claiming the planned five-trial repeatability
result.
