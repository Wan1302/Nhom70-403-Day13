# Day 13 Observability Lab Report

> **Instruction**: Fill in all sections below. This report is designed to be parsed by an automated grading assistant. Ensure all tags (e.g., `[GROUP_NAME]`) are preserved.

## 1. Team Metadata
- [GROUP_NAME]: Nhom70-403-Day13
- [REPO_URL]: https://github.com/Wan1302/Nhom70-403-Day13
- [MEMBERS]:
  - Member A: 2A202600080 - Hồ Trần Đình Nguyên | Role: Logging & PII
  - Member B: 2A202600081 - Hồ Trọng Duy Quang | Role: Tracing & Enrichment
  - Member C: 2A202600057 - Hồ Đắc Toàn | Role: Load Test & Dashboard

---

## 2. Group Performance (Auto-Verified)
- [VALIDATE_LOGS_FINAL_SCORE]: 100/100
- [TOTAL_TRACES_COUNT]: 
- [PII_LEAKS_FOUND]: 0

---

## 3. Technical Evidence (Group)

### 3.1 Logging & Tracing
- [EVIDENCE_CORRELATION_ID_SCREENSHOT]: docs/screenshot_correlation_id.png
- [EVIDENCE_PII_REDACTION_SCREENSHOT]: docs/screenshot_pii_redaction.png
- [EVIDENCE_TRACE_WATERFALL_SCREENSHOT]: [Path to image]
- [TRACE_WATERFALL_EXPLANATION]: (Briefly explain one interesting span in your trace)

### 3.2 Dashboard & SLOs
- [DASHBOARD_6_PANELS_SCREENSHOT]: docs/evidence/dashboard-6-panels.png
- [SLO_TABLE]:
| SLI | Target | Window | Current Value |
|---|---:|---|---:|
| Latency P95 | < 3000ms | 28d | 2650ms |
| Error Rate | < 2% | 28d | 0.0% |
| Cost Budget | < $2.5/day | 1d | $0.012972 |
| Quality Score Avg | >= 0.75 | 28d | 0.90 |

### 3.3 Alerts & Runbook
- [ALERT_RULES_SCREENSHOT]: config/alert_rules.yaml
- [SAMPLE_RUNBOOK_LINK]: docs/alerts.md#1-high-latency-p95

---

## 4. Incident Response (Group)
- [SCENARIO_NAME]: rag_slow
- [SYMPTOMS_OBSERVED]: Sau khi bật incident `rag_slow`, request `/chat` vẫn trả về thành công nhưng latency tăng rõ rệt. Log `response_sent` của request kiểm thử có `latency_ms=2650`, cao hơn nhiều so với các request bình thường khoảng 150ms. Đây là dấu hiệu tail latency tăng do bước retrieval/RAG bị chậm, trong khi error rate vẫn là 0.0%.
- [ROOT_CAUSE_PROVED_BY]: Metrics cho thấy Latency P95 tăng lên 2650ms trong lần kiểm thử incident. Log evidence: `correlation_id=req-rag-slow-rca`, `event=response_sent`, `latency_ms=2650`, `session_id=s_incident_rca`, `feature=qa`, `model=claude-sonnet-4-5`. Trong code, scenario `rag_slow` bật `STATE["rag_slow"]`, làm `app/mock_rag.py::retrieve()` sleep 2.5 giây trước khi trả context, nên root cause nằm ở RAG retrieval span chứ không phải LLM generation hay schema validation.
- [FIX_ACTION]: Tắt incident bằng endpoint `/incidents/rag_slow/disable`, chạy lại request/load test để xác nhận latency quay về mức bình thường, sau đó kiểm tra lại `python scripts/validate_logs.py` để bảo đảm log schema, enrichment, correlation ID và PII redaction vẫn đạt.
- [PREVENTIVE_MEASURE]: Duy trì alert `high_latency_p95`, dashboard panel Latency P50/P95/P99, và runbook `docs/alerts.md#1-high-latency-p95`. Thêm timeout/fallback cho retrieval, giới hạn prompt/context dài, theo dõi Langfuse trace waterfall để tách riêng RAG span và LLM span khi latency tăng.

---

## 5. Individual Contributions & Evidence

### 2A202600080 - Hồ Trần Đình Nguyên
- [TASKS_COMPLETED]: Implement Correlation ID middleware, PII scrubbing processor, bổ sung PII patterns (passport, địa chỉ VN)
- [EVIDENCE_LINK]: https://github.com/Wan1302/Nhom70-403-Day13/commit/0ffe61a

### 2A202600081 - Hồ Trọng Duy Quang
- [TASKS_COMPLETED]: Implemented `/chat` log enrichment by binding `user_id_hash`, `session_id`, `feature`, `model`, and `env` into structlog context variables; verified that API logs contain correlation context without exposing raw user identifiers; finalized SLO targets in `config/slo.yaml`; reviewed and expanded alert rules in `config/alert_rules.yaml`; updated runbook details in `docs/alerts.md`.
- [EVIDENCE_LINK]: 

### 2A202600057 - Hồ Đắc Toàn
- [TASKS_COMPLETED]: 
- [EVIDENCE_LINK]: 

---

## 6. Bonus Items (Optional)
- [BONUS_COST_OPTIMIZATION]: (Description + Evidence)
- [BONUS_AUDIT_LOGS]: (Description + Evidence)
- [BONUS_CUSTOM_METRIC]: (Description + Evidence)
