
# Certificate Manger

## Create MAP
```
gcloud certificate-manager maps create gke
```

## Import selfsigned certificate
```
gcloud certificate-manager certificates create  gke-demo \
    --certificate-file="gke.demo.crt" \
    --private-key-file="gke.demo.key" 
```
## Create default map entry
```
gcloud certificate-manager maps entries create default \
    --map=gke \
    --certificates=gke-demo \
    --set-primary
```