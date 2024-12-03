# GKE Challenge lab

This lab will let you touch base with the basics to get a Google Kubernetes Cluster running and serve a small web service. 


To make it easy - use the Cloud Shell for being able to execute commands.

## Create a docker artifact repository (1)

Create a docker type artifact repository called images in europe-west4

Repo info:</br>
type: docker</br>
region: europe-west4</br>
name: images</br>

https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images

## Create a sample container and build with cloud build (2)

Build container image with cloud build and push to the created repo, call the image hello-app.

Image info:</br>
name: europe-west4-docker.pkg.dev/${PROJECT_ID}/images/hello-app</br>
source code: src/hello-app</br>

https://cloud.google.com/sdk/gcloud/reference/builds/submit

## Deploy container to Cloud Run (3)

Build container image with cloud build and push to the created repo, call the image hello-app.

Image info:</br>
name: europe-west4-docker.pkg.dev/${PROJECT_ID}/images/hello-app</br>
service-name: hello-app
Allow-unauthenticated: yes
region: europe-west4

Verify container can be accessed from run.app url

https://cloud.google.com/run/docs/deploying

## Create VPC & Subnet (4)

Create a custom mode VPC Network in your project name it *vpc*

VPC info:</br>
name: vpc</br>
routing mode: global</br>
subnet-mode: custom


Subnet info:</br>
name: primary-subnet</br>
region: europe-west4</br>
cidr: 10.0.0.0/24</br>

https://cloud.google.com/vpc/docs/create-modify-vpc-networks


## Provision GKE Cluster (5)

Create a VPC cluster in any of the zones in europe-west4, using release channel regular

Cluster info:</br>
name: gke</br>
release-channel: regular</br>
zone: europe-west4-(a|b|c)</br>
gateway-api: standard</br>
network: vpc</br>
subnet: primary-subnet

Leaves others to default.

https://cloud.google.com/kubernetes-engine/docs/how-to/creating-a-zonal-cluster

## Connect to GKE (6)

Connect to GKE using cloud shell.

Verify using:</br>
```
kubectl get nodes
```

## Create namespace in GKE (7)

Create a namespace called hello-app in GKE

Verify with:
```
kubectl describe namespace hello-app
```

https://cloud.google.com/kubernetes-engine/docs/learn/get-started-with-kubernetes


## Create Deployment in GKE (8)

Create a deployment in GKE with the following options:</br>
namespace: hello-app</br>
name: hello-deployment</br>
labels: app=hello-app</br>
image: (the image returned by cloud-build)</br>
container_name: hello-app</br>
ports: 8080 port named http</br>
replicas: 1</br>

Optional: 
- Livenessprobe
- Readinessprobe

Verify using:
```
kubectl -n hello-app get pods -o wide
```
IP address from above is needed in next step.

https://cloud.google.com/kubernetes-engine/docs/learn/get-started-with-kubernetes


## Create Service in GKE (9)

namespace: hello-app</br>
name: hello-service</br>
labels: app=hello-app</br>
type: ClusterIP</br>
port: 8080 -> http in deployment</br>
selector: app=hello-app</br>

Verify using:
```
kubectl -n hello-app describe service hello-service
```
(should containing one endpoint which is the ip/port of the pod/container, which can be seen from the previous verification)

https://cloud.google.com/kubernetes-engine/docs/learn/get-started-with-kubernetes

## Test using port-forward (10)

In Cloud Shell use Web Preview function to test on port 8080.

```
kubectl -n hello-app port-forward service/hello-service 8080:8080
```

Verify you have connection before proceeding to next step.

## Create certificate manager map, self-signed cert and certificate map entry (11)

- Create a certificate manager map
- Create a self-signed certificate.
- Import the self-signed certificate.
- Create default map entry for the self-signed certificate.


certificate-manager-map-name: gke</br>
certificate-name: gke-demo</br>
certificate-location: global</br>
certificate-map-entry-name: default</br>



Create self-signed certificate:
```
openssl req -x509 -newkey rsa:2048 -sha256 -days 365   -nodes -keyout gke.demo.key -out gke.demo.crt -subj "/CN=gke.demo"   -addext "subjectAltName=DNS:gke.demo"
```

https://cloud.google.com/certificate-manager/docs/certificates</br>
https://cloud.google.com/certificate-manager/docs/maps</br>
https://cloud.google.com/certificate-manager/docs/map-entries</br>


## Deploy Gateway (12)

info:</br>
name: gke-demo-load-balancer</br>
namespace: default</br>
type: gke-l7-global-external-managed</br>
cert-map: gke</br>
port: 443</br>
protocol: HTTPS</br>

Allow HTTPRoutes from all namespaces.

Verify using:
```
kubectl get gateway gke-demo-load-balancer
kubectl describe gateways gke-demo-load-balancer
curl -vk https://${IP_ADDRESS}
```

https://cloud.google.com/kubernetes-engine/docs/how-to/deploying-gateways#external-gateway

## Deploy HTTPRoute (13)


info:</br>
name: hello-route</br>
namespace: hello-app</br>
service: hello-service</br>
port: 8080</br>
hostname: gke.demo</br>

Verify using:
````
kubectl -n hello-app describe httproute hello-route
curl -vk --resolve 'gke.demo:443:${IP_ADDRESS}' https://gke.demo/
````

https://cloud.google.com/kubernetes-engine/docs/how-to/deploying-gateways#external-gateway


## Create Cloud Armor (14)

Create a security policy.
Create a rule with priority 1000, that blocks WAF Rule lfi-v33-stable with 403

Info:
Name: security-policy-gke

https://cloud.google.com/armor/docs/configure-security-policies


## Attach Cloud Armor Policy to Service (15)

Attach Policy to Service (hello-service in hello-app)


Verify using:
```
curl -vk --resolve 'gke.demo:443:${IP_ADDRESS}' https://gke.demo/.env
```

https://cloud.google.com/kubernetes-engine/docs/how-to/configure-gateway-resources#configure_cloud_armor