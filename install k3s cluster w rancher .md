```
ytc-master-1 = ip = r
ytc-node-1   = ip = r
ytc-node-2   = ip = r
```

### update system
```
apt-get update && apt-get upgrade -y
```

### open this ports on all nodes
```
ufw allow 6443
ufw allow 8472
ufw allow 10250
ufw allow 2379:2380/tcp
```

### install master k3s
```
curl -sfL https://get.k3s.io | sh -s - \
  --node-name ytc-master-1 \
  --no-deploy traefik \
  --write-kubeconfig-mode 644 \
  --kubelet-arg node-status-update-frequency=5s \
  --kube-controller-arg node-monitor-period=2s \
  --kube-controller-arg node-monitor-grace-period=20s \
  --kube-apiserver-arg default-not-ready-toleration-seconds=30 \
  --kube-apiserver-arg default-unreachable-toleration-seconds=30
```

### get token
```
cat /var/lib/rancher/k3s/server/node-token
```

### install k3s agent to workers
```
curl -sfL https://get.k3s.io | K3S_NODE_NAME=ytc-node-1 K3S_URL=https://<master-ip>:6443 K3S_TOKEN=<token> sh -s - \
  --kubelet-arg node-status-update-frequency=5s
curl -sfL https://get.k3s.io | K3S_NODE_NAME=ytc-node-2 K3S_URL=https://<master-ip>:6443 K3S_TOKEN=<token> sh -s - \
  --kubelet-arg node-status-update-frequency=5s
```

### install k3s update agent

```
kubectl apply -f https://github.com/rancher/system-upgrade-controller/releases/latest/download/system-upgrade-controller.yaml
```
Then copy plans: https://docs.k3s.io/upgrades/automated#configure-plans
<br>`kubectl -n system-upgrade apply -f upgrade-plans.yml`

### install cert-manager https://cert-manager.io/docs/installation/helm/
```
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.8.0 \
  --set installCRDs=true
```
apply letsencrypt-staging and letsencrypt-prod

### install nginx https://kubernetes.github.io/ingress-nginx/deploy/#quick-start
```
helm upgrade \
  --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

### (optional) install rancher https://docs.technotim.live/posts/rancher-ha-install/
```
helm install \
  rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.r.vreau.eu \
  --set bootstrapPassword=<ui-password> \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=<email> \
  --set letsEncrypt.ingress.class=nginx \
  --create-namespace
```

### (optional) install argocd
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### (optional) install docker
```
apt-get remove docker docker-engine docker.io containerd runc \
; apt-get install ca-certificates curl gnupg lsb-release \
&& curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg \
&& echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null \
&& apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io
```
