# rasp-k3s-fluxcd

# Introduction
This project is a prototype setup for building a complete development workflow using FluxCD. It includes local development, CI builds, cluster synchronization, secret management, HTTPS certificates, Traefik Ingress, custom operators, CRDs, and more. The first goal is to set up the local development environment and the build/deploy pipeline, after which one or more applications will be deployed. The design and the applications will become clearer as the project evolves. Once the setup proves useful, the project will move from a small Raspberry Pi cluster with an external database to a cloud environment.

# Components
1. Kubernetes Cluster: A k3s deployment running on Raspberry Pi nodes.
2. Cluster Management UI: OpenLens connected to the k3s cluster.
3. Access Control: A read-only Kubernetes service account for limited-access management.
4. Domains & DNS Configuration: Proper DNS records configured for external access to cluster services.
5. Home Router Setup: Port forwarding rules for ports 80, 443, and 6443 to enable ingress traffic and API server access.
6. TLS Configuration: A self-signed certificate for the cluster’s API endpoint (e.g., myhost.com:6443) including appropriate SAN entries.
7. Readonly kubernetes dashboard

8. 
# ToDo
- [x] Kubernetes running on Raspberry pi
- [ ] Configure CI pipeline  
- [ ] Deploy first application 


## Setup steps:
1. Install Flux on your local machine (My local Windows machine)

2. Make sure you can access your k8s cluster from your local machine. Test using kubectl. 

3. Install Flux e.g. `choco install flux`

4. Generate github personal token on github web page -- expiry date in one year. The expiry date can be customized.

5. Run bootstap command on your local. It will create Flux resources in your k8s cluster.
```
export GITHUB_TOKEN=<>
flux bootstrap github \
  --token-auth \
  --owner=joehuangcoding \
  --repository=rasp-k3s-fluxcd \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
  ```

6. Command 5 created ./cluster/my-cluster/flux-system


## Deployment your first app using fluxcd. Example here deloying cert-manager into the cluster


## Commands
```
// Reconcile source/helm chart first then kustomization and helmrelease


flux reconcile source git <git-repo-name> --namespace <namespace>

flux reconcile source helm <helm-repo-name> --namespace <namespace>

flux reconcile kustomization <kustomization-name> --namespace <namespace>

flux reconcile helmrelease <helmrelease-name> --namespace <namespace>

e.g.



flux reconcile source git flux-system --namespace flux-system 

flux reconcile kustomization flux-system --namespace flux-system

flux reconcile kustomization infrastructure --namespace flux-system

flux reconcile kustomization nodejs-hello-world --namespace flux-system

// change the version 0.1.2 -> 0.1.3 in Chart.yaml
flux reconcile helmrelease nodejs-hello-world --namespace flux-system

--with-source: Reconcile the source (e.g., GitRepository or HelmRepository) before reconciling the resource.
--dry-run: Preview the changes without applying them.
```

## Kubernetes-dashboard Token
```
// Expired in a hour
kubectl -n NAMESPACE create token SERVICE_ACCOUNT
```

### Secret based token
```
apiVersion: v1
kind: Secret
metadata:
  name: dashboard-readonly-token
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: dashboard-readonly
type: kubernetes.io/service-account-token

//Read the token
kubectl -n kubernetes-dashboard get secret dashboard-readonly-token -o jsonpath='{.data.token}' | base64 -d
```