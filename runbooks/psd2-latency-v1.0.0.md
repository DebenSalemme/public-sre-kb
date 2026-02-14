---
service: psd2
domain: payments
env: prod
platforms: [grafana, azure-monitor, openshift]
topics: [latency, saturation]
owner: sre-team
last_review: 2026-02-14
version: 1.0.0
confidentiality: public
---

# Runbook — Latenza PSD2 (Prod)

## Sintomi
- P99 > 1.2s per >10 min

## Ipotesi principali
1) Saturazione pod `psd2-api`
2) Degrado gateway pagamenti
3) Regressione da deploy

## Query rapide (PromQL)
```
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket{job="psd2-api"}[5m])) by (le))
```

## KQL (Azure Monitor)
```kusto
AppRequests
| where Cloud_RoleName == "psd2-api"
| summarize p99 = percentile(DurationMs, 99) by bin(Timestamp, 5m)
```

## OpenShift – Diagnostica
```bash
oc project prod
oc get pods -l app=psd2-api -o wide
oc describe deploy/psd2-api
oc adm top pods -n prod | sort -k3 -nr | head
oc logs deploy/psd2-api --tail=300
```

## Azioni consigliate
- [ ] Scala repliche se CPU>80% e p99>1.2s
- [ ] Rollback ultima release se correlata

## Verifiche finali
- [ ] p99 < 1.2s per 15 min
- [ ] 5xx < 1%
