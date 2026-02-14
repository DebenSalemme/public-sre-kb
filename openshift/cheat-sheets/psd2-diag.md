
# PSD2 — Diagnostica OpenShift (Prod)
```bash
oc project prod
oc get pods -l app=psd2-api -o wide
oc describe deploy/psd2-api
oc get hpa psd2-api
oc describe hpa psd2-api
oc logs deploy/psd2-api --tail=300
```
