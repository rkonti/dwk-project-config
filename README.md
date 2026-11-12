# dwk-project-config

Deployment configuration for the todo project of the University of Helsinki course
[DevOps with Kubernetes](https://devopswithkubernetes.com/). The application code lives in
[rkonti/DevOps-with-Kubernetes](https://github.com/rkonti/DevOps-with-Kubernetes).

Argo CD deploys this repository to the cluster:

| Environment | Namespace | Kustomize overlay | Updated when |
|---|---|---|---|
| Staging | `staging` | `environments/staging` | a commit is pushed to `main` in the code repo |
| Production | `production` | `environments/production` | a tag is pushed in the code repo |

The code repo's release workflow builds the images, pushes them to `ghcr.io/rkonti`, and commits
the new image tags to the matching overlay here. Argo CD then syncs the change.

## Layout

```
todo-app/, todo-backend/, broadcaster/   manifests of each service
environments/base/                       combines the services
environments/staging/                    staging overlay: broadcaster only logs, no backups
environments/production/                 production overlay: daily database backup
argocd/                                  Argo CD Applications for both environments
```

## Setup

```bash
kubectl apply -f argocd/
```

Secrets that are not in Git are created by hand, for example the Telegram credentials of the
production broadcaster:

```bash
kubectl -n production create secret generic broadcaster-secret \
  --from-literal=TELEGRAM_BOT_TOKEN='<token>' \
  --from-literal=TELEGRAM_CHAT_ID='<chat id>'
```
