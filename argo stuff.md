# argo stuff

### install kubeseal on cluster
```
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.17.4/controller.yaml
```

### install kubeseal cli
```
https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.17.4/kubeseal-0.17.4-windows-amd64.tar.gz
```

### encrypt a secret
```
kubectl -n youtubegaminglive \
  create secret generic soketi \
  --dry-run=client \
  --from-literal PUSHER_APP_ID=foo \
  --from-literal PUSHER_APP_KEY=bar \
  --from-literal PUSHER_APP_SECRET=ultrasecret \
  --output yaml | kubeseal -o yaml | tee secrets.yml
```


### build docker images and push to github with kaniko
```
kubectl -n argo-events \
  create secret \
  docker-registry regcred \
  --docker-server=ghcr.io \
  --docker-username=AndreiTelteu \
  --docker-password=github-token-here \
  --docker-email=github-email-here
```

### example build config:
```
./build-soketi.yml
```

### build docker images and push to github with kaniko
```
kubectl -n argo-events \
  create secret \
  generic github-token \
  --from-literal token="secret-token" \
  --from-literal email="secret-email" \
  --from-literal user="secret-name" \
```

### install argo workflows https://github.com/argoproj/argo-workflows/releases/tag/v3.3.1
```
kubectl create namespace argo
kubectl apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.3.1/install.yaml
```

### expose argo ingress with basic auth (keep auth as filename)
```
htpasswd -c auth username
kubectl -n argo create secret generic argo-basic-auth --from-file=auth
./argo-workflows.yml
```

### argo patch auto mode
```
kubectl -n argo patch \
  deployment argo-server \
  -p '{"spec":{"template":{"spec":{"containers":[{"name":"argo-server","args": [ "server", "--auth-mode", "server" ]}]}}}}'
```

### if you need to undo a change
```
kubectl -n argo rollout history deployment/argo-server
kubectl -n argo rollout undo deployment/argo-server --to-revision=1
```

### install argo events https://argoproj.github.io/argo-events/installation/
```
kubectl create namespace argo-events
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-events/stable/manifests/install.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml
```
