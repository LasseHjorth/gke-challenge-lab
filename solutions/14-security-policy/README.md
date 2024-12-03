
## Create Security Policy

```
gcloud compute security-policies create security-policy-gke
```


## Create Security Rule

```
gcloud compute security-policies rules create 1000 \
    --security-policy gke-security-policy \
    --expression "evaluatePreconfiguredExpr('lfi-v33-stable')" \
    --action deny-403 \
    --description "LFI Block"
```