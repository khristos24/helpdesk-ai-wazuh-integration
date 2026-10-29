# HelpDesk AI / Wazuh integration handoff

This public repository is the versioned transfer point between the HelpDesk AI
application server (Server A) and the Wazuh SIEM server (Server B).

## Contents

- `integration-contract/`: authoritative security-event schema, synthetic rule-test
  fixtures, scenario mapping, and checksums.
- `server-a/wazuh-agent/`: the application log-collection fragment to merge into the
  Server A Wazuh agent configuration.
- `server-a/evidence/`: sanitized application-side validation results. These do not
  prove Wazuh ingestion or alerting.
- `server-b/`: instructions and, later, versioned Wazuh rules, test events,
  dashboards, and runbooks produced on Server B.

Current contract version: `1.0`

Authoritative schema SHA256:

```text
f1ed71186058bfbeb3ba900fb7f4b6d99deede80258b829d3380b0ce75215b48
```

Verify a checkout before using it:

```bash
cd integration-contract
sha256sum -c SHA256SUMS
```

Server A owns the meaning and version of the event contract. Server B must build
detections against the published version and must not silently reinterpret fields.
Breaking schema changes require a new version and coordinated rollout.

This repository must never contain passwords, API tokens, Wazuh client keys,
enrollment secrets, private keys, private host addresses, or unredacted application
logs. The events in `example-events.jsonl` are invented fixtures.
