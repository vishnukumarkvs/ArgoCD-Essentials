Notes: Installation options

You need to create a namespace for argocd.

kubectl create ns argocd

and then choose one of the below options :

1. Non-HA :
a. cluster-admin privileges: https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
b. namespace level privileges: https://github.com/argoproj/argo-cd/raw/stable/manifests/namespace-install.yaml

2. HA:
a. cluster-admin privileges: https://github.com/argoproj/argo-cd/raw/stable/manifests/ha/install.yaml
b. namespace level privileges: https://github.com/argoproj/argo-cd/raw/stable/manifests/ha/namespace-install.yaml

3. Light installation "Core"https://github.com/argoproj/argo-cd/raw/stable/manifests/core-install.yaml

4. Helm chart: https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd
---
https://gist.github.com/dcormier/8b27e61bead4d7293640ad2697647a3f - docker cleaup commands
---
To change kubectl cluster,  use
kubectl config use-context docker-desktop (kubectl config --help)

---
Install argocd
kubectl create ns argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl get all -n argocd

---
Need to login using initial password
kubectl get secret -n argocd
kubectl get secret -n argocd argocd-initial-admin-secret -o yaml
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String('<password>'))

---
expose server (port forward or ingress or load balancer)

kubectl get svc -n argocd
kubectl port-forward svc/argocd-server -n argocd 8080:443
localhost:8080
username: admin, passowrd: initial converted password

---
Argocd cli

official doc: https://argo-cd.readthedocs.io/en/stable/cli_installation/

Expose argocd server with port forward
argocd version
argocd login localhost:8080 & provide username , password
argocd cluster list
---

Argocd example apps
https://github.com/argoproj/argocd-example-apps

---
Create an argocd application using yaml
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  destination:
    namespace: guestbook
    server: "https://kubernetes.default.svc"
  project: default
  source:
    path: guestbook
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: master
  syncPolicy:
    syncOptions:
      - CreateNamespace=true
```
kubectl apply -f application.yaml
kubectl get ns
kubectl get all -n guestbook
kubectl get svc -n guestbook
kubectl port-forward svc/guestbook-uir -n guestbook 8081:80

- server: "https://kubernetes.default.svc" --> This means the cluster wil be the one argocd runs in

---
create application using argocd cli

argocd app create app-1 --repo https://github.com/argoproj/argocd-example-apps.git --revision master --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace app-1 --sync-option CreateNamespace=true
argocd app list
argocd app sync app-1

---

Parameters
- Directory
- Helm
- Kustomize
- Plugin
Parameters can be automatically detected from repo.

By explicitly using a parameter, we can declare several options.
Helm options:-
```
helm:
      releaseName: my-release
```
Directory options:-
```
directory:
      recurse: true
```
This will include all files in sub-directories.

---
Projects
- Logical grouping of applications
