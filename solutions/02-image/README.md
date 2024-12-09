
# Cloud Build

Simple Cloud Build submit.

```
gcloud builds submit --tag europe-west4-docker.pkg.dev/${PROJECT_ID}/images/hello-app .
```


Alternative:
```
gcloud auth configure-docker europe-west4-docker.pkg.dev
docker build --tag europe-west4-docker.pkg.dev/${PROJECT_ID}/images/hello-app .
docker push europe-west4-docker.pkg.dev/${PROJECT_ID}/images/hello-app
```