# Deployment

```
PROJECT_ID=$DEVSHELL_PROJECT_ID envsubst < deployment.yaml | kubectl -n hello-app apply -f -
```