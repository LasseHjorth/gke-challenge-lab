apiVersion: networking.gke.io/v1
kind: GCPBackendPolicy
metadata:
  name: gke-backend-policy
spec:
  default:
    securityPolicy: security-policy-gke
  targetRef:
    group: gateway.networking.k8s.io
    kind: Gateway
    name: gke-demo-load-balancer
