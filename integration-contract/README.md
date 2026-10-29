# HelpDesk AI / Wazuh contract 1.0

Share this entire directory with the Wazuh-server operator before finalizing rules.
The source path is `/home/ubuntu/helpdesk-ai-lab/integration-contract/`.
`security-event-schema.json` is authoritative; `lib/security-telemetry.ts` derives
its runtime field validation from that file. Breaking changes require a new version
and coordinated rollout. `example-events.jsonl` contains **invented fixtures**, never
proof of application execution or manager ingestion. Do not append examples to real logs.

Each event is one flat JSON object on one line. Required nullable identifier fields
use `null` for unavailable/not applicable; never infer an owner from a requested ID.
Optional fields are absent when unknown: `run_id` (no chat run), `scenario_id`
(no trusted instructor attribution), `document_id` (not a retrieval), `model_id`
(no model path), `event_stage`, `latency_ms`. Optional identifier fields can be null
when an observed value fails identifier validation. Production code omits
`scenario_id`: client chat labels remain only legacy instructor annotations and
are not security evidence. Times are UTC; event/request/run IDs are server UUIDs.
Actors come from verified signed sessions. Mode comes from the server environment.

`execution_source=live_model` denotes the live application request path. For
`chat_history_access` it denotes direct HTTP access, **not** model participation.
`dispatcher_test` means deterministic backend execution; `recorded_replay` is
reserved for replay tooling. No caller can select these through the chat API.
`model_tool_requested` alone proves neither authorization nor database execution.
`authorization_result=not_evaluated` explicitly records deliberate vulnerable
policy bypass. Secure ticket authorization follows the scoped DB lookup; unavailable
resources are denied without performing an extra cross-user ownership lookup.
A failed DB call is `failed`, never a denied request or successful persisted write.
`tool_executed` marks completed adapters, including reads that return null/empty data;
resource presence and private-data exposure require their own evidence.

Exposure booleans are local to the event, not cumulative run state. `false` means
this event does not establish the outcome, not proof that the entire run was safe.
Private fields returned by an adapter are classified but not labeled model exposure
until the next model request receives a valid inference response. A timeout is inconclusive
about remote receipt. A valid inference response is delivery evidence, not proof of model
attention or retention. Sensitive history detection is limited to known synthetic
markers; arbitrary private text is not classified by this detector.
Disclosure requires a known fixture marker observed in a successful tool result
and in the final answer, excluding markers present in user history. Marker values
and response bodies are never written to these JSONL files. Multiple classifications
can produce separate final verification events. No final event means disclosure
was not established by this verifier; it does not prove no disclosure occurred.

`attack_indicator` describes observed access/exposure, not a universal compromise
verdict. Normal own-ticket operations and authorized staff access have `none`.
A poison document alone has `none`. `suspected_indirect_injection` requires its
retrieval and submission to a subsequent inference before a successful cross-user profile operation during the same live
request. It establishes correlation, not causality; verify final disclosure separately.
S03 own-profile overcollection is `excessive_fields`, not cross-user compromise.
Rules should also match `application`, `schema_version`, `event_type`, `app_mode`,
`execution_source`, and `execution_result`; do not alert on keywords or scenario IDs.

Writer limits: one process per instance file; 256 queued lines, 8192 bytes per line,
10 MiB active file plus three rotated files. Local asynchronous writes are serialized.
Overflow/invalid/write failures emit fixed diagnostic codes and increment health
counters. This is best-effort telemetry, not a durable audit queue. Abrupt termination,
queue overflow, disk failures, or collector downtime can lose events. Monitor
`/api/health` telemetry counters. Do not run multiple writers against the same mount.
