# Create GKE Cluster


```
gcloud container clusters create gke \
    --release-channel regular \
    --zone europe-west4-a \
    --network vpc \
    --subnetwork primary-subnet \
    --node-locations europe-west4-a \
    --gateway-api=standard
```
