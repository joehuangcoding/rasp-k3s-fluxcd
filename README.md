# rasp-k3s-fluxcd

# Introduction
This project is a prototype setup for building a complete development workflow using FluxCD. It includes local development, CI builds, cluster synchronization, secret management, HTTPS certificates, Traefik Ingress, custom operators, CRDs, and more. The first goal is to set up the local development environment and the build/deploy pipeline, after which one or more applications will be deployed. The design and the applications we choose will become clearer as the project evolves. Once the setup proves useful, the project will move from a small Raspberry Pi cluster with an external database to a cloud environment.

# 


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
