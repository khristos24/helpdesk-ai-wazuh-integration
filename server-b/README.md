# Server B deliverables

Server B should verify `integration-contract/SHA256SUMS` before building rules.

Expected additions from the Wazuh deployment and detection session:

```text
server-b/
├── deployment/
├── rules/
├── test-events/
├── dashboards/
└── runbooks/
```

Rules must match the authoritative schema and distinguish `live_model`,
`dispatcher_test`, and `recorded_replay`. Synthetic fixtures can prove rule logic,
but only a genuine event with a matching application request ID, Wazuh agent ID,
rule ID, and indexed-alert reference proves end-to-end integration.

Do not commit Wazuh credentials, client keys, enrollment passwords, API tokens,
private certificates, private addresses, or raw production logs to this public
repository.
