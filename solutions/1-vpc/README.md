# Create VPC

```
gcloud compute networks create vpc \
    --subnet-mode=custom \
    --bgp-routing-mode=regional
```


# Create subnet

```
gcloud compute networks subnets create primary-subnet \
    --network=vpc \
    --range=10.0.0.0/24 \
    --region=europe-west4
```