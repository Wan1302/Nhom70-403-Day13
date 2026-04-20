# Person 2 Handoff: Enrichment, SLOs, Alerts

## Verification status
- Log enrichment fields expected on `/chat` API logs:
  - `correlation_id`
  - `env`
  - `user_id_hash`
  - `session_id`
  - `feature`
  - `model`
- Validation command:
  - `python scripts/validate_logs.py`
- Target result:
  - `Estimated Score: 100/100`
  - `Potential PII leaks detected: 0`

## Evidence for Person 3
- `docs/evidence/validate-logs-100.png`: screenshot of `python scripts/validate_logs.py`.
- `docs/evidence/log-correlation-id.png`: screenshot showing `request_received` and `response_sent` with the same `correlation_id`.
- `docs/evidence/pii-redaction.png`: screenshot showing `[REDACTED_EMAIL]`, `[REDACTED_PHONE_VN]`, and `[REDACTED_CREDIT_CARD]`.
- `docs/evidence/alert-rules.png`: screenshot of `config/alert_rules.yaml` and/or alert UI with runbook links.

## SLOs prepared
- Latency P95: less than 3000ms.
- Error rate: less than 2%.
- Daily cost: less than $2.5/day.
- Average quality score: at least 0.75.

## Alert rules prepared
- `high_latency_p95`: latency breach investigation.
- `high_error_rate`: API failure investigation.
- `cost_budget_spike`: cost and token usage investigation.
- `low_quality_score`: quality regression investigation.

## Suggested blueprint contribution text
Implemented log enrichment for `/chat` by binding `user_id_hash`, `session_id`, `feature`, `model`, and `env` into structlog context variables. Verified that API logs include correlation context and request metadata without exposing raw user identifiers. Also finalized SLO targets and alert rules with runbook links for latency, errors, cost, and quality regressions. Validation result: `validate_logs.py` reached the target score with no detected PII leaks.

