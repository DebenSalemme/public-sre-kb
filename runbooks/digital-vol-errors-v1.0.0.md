---
service: digital-vol
domain: digital
env: prod
platforms: [grafana, azure-monitor, openshift]
topics: [error_rate, availability]
owner: sre-team
last_review: 2026-02-14
version: 1.0.0
confidentiality: public
---

# Runbook — Errori 5xx Digital VOL (Prod)

## Sintomi
- Error rate >1%

## Ipotesi principali
1) Bug applicativo
2) Degrado dipendenze
3) Config errata

## Query rapide (PromQL)
```
sum(rate(http_requests_total{job="digital-vol-api",status=~"5.."}[5m])) / sum(rate(http_requests_total{job="digital-vol-api"}[5m]))
```

## KQL (Azure Monitor)
```kusto
AppRequests
| where Cloud_RoleName == "digital-vol-api" and toint(ResultCode) >= 500
| summarize errors=count() by ResultCode, bin(Timestamp, 5m)
```

## OpenShift – Diagnostica
```bash
oc project prod
oc get pods -l app=digital-vol-api -o wide
oc logs deploy/digital-vol-api --tail=400
```

## Azioni consigliate
- [ ] Confronta build corrente vs precedente
- [ ] Verifica dipendenze DB/API
- [ ] Attiva feature flag safe

## Verifiche finali
- [ ] 5xx < 1% per 30 min
