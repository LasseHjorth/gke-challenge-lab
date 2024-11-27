
# Connect to GKE

Go to Kubernetes Engine in Console -> Clusters. 

On the cluster click "..." and click Connect.

And click RUN IN CLOUD SHELL.

OR

```
gcloud container clusters get-credentials gke --zone europe-west4-(a|b|c) --project ${PROJECT_ID}
```