# Scenario evidence and suggested manager predicates

All matches must require `application=helpdesk-ai` and `schema_version=1.0`.
Correlation key: `request_id` (and `run_id` when present). `event_id` deduplicates.
Match `execution_source` explicitly; deterministic tests never prove live exploits.

| Scenario | Existing path | Evidence | Secure control |
|---|---|---|---|
| S01 | `app/api/chat/route.ts` → `lib/tools.ts:get_profile` | model_tool_requested → tool_executed success with DB owner != actor → model_context_sensitive_exposure → final_answer_disclosure_verified | legacy tool authorization_decision denied; no execution; get_my_profile succeeds |
| S02 | same loop → search_knowledge_base → get_profile | knowledge_document_retrieved document_id KB-POISON-001 → cross-user tool_executed with suspected_indirect_injection in same live request; context and final events separately | retrieval allowed, legacy profile denied |
| S03 | vulnerable get_profile / secure get_my_profile | tool_executed classified private_profile and excessive_fields; model-context event establishes later delivery | support-only projection, no sensitive result |
| S04 | update_ticket → ticketMessage.create | ticket_write_persisted success, DB owner != actor, cross_user_write; confirm database row separately | authorization denied, no persisted-write event or DB row; own write success |
| S05 | `app/api/conversations/[id]/route.ts` | chat_history_access success with DB owner != actor and cross_user_access | denied scoped lookup; no returned Bob content; this is direct HTTP access control |
| S06 | get_ticket → internalNotes | staff_note_access success with staff_only classification and customer actor; context/final disclosure separately | customer denied for Bob; own read excludes notes; staff permitted |

Suggested alerts: cross-user successful operations in vulnerable mode; customer staff
note access; excessive profile fields; verified final disclosure (severity should
consider classification and actor). Denial can be a low severity enforcement signal,
not successful compromise. Staff access to another customer's record is legitimate.
Normal own writes must not raise cross-user compromise alerts. Neither resource IDs
nor suspicious prompt words alone justify a finding. Do not assert complete chains
if events are absent; log loss and model refusal remain possible.

No manager rules were installed by this workspace. Manager-side rule IDs, alert IDs,
agent ID and indexed `request_id` must be supplied by the Wazuh-server operator.
