
# Digital VOL — Diagnostica OpenShift (Prod)
```bash
oc project prod
oc get pods -l app=digital-vol-api -o wide
oc describe deploy/digital-vol-api
oc get hpa digital-vol-api
oc describe hpa digital-vol-api
oc logs deploy/digital-vol-api --tail=300
```
