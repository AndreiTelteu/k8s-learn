
I tried to uninstall rancher ui and it got stuck in terminating state.
So i found this method to remove everything


```
JWT_TOKEN_KUBESYSTEM_DEFAULT=$(kubectl -n kube-system get secrets \
    $(kubectl -n kube-system get serviceaccounts/namespace-controller -o jsonpath='{.secrets[0].name}') \
    -o jsonpath='{.data.token}' | base64 --decode)
```

```
rancher                                  Terminating   19h
cattle-system                            Terminating   19h
cattle-fleet-system                      Terminating   19h
cattle-fleet-local-system                Terminating   19h
p-wmpdk                                  Terminating   19h
p-nnsqm                                  Terminating   19h
cattle-impersonation-system              Terminating   19h
cattle-global-data                       Terminating   19h
cattle-global-nt                         Terminating   19h
cattle-fleet-clusters-system             Terminating   19h
cluster-fleet-local-local-1a3d67d0a899   Terminating   19h
fleet-default                            Terminating   19h
fleet-local                              Terminating   19h
local                                    Terminating   19h
```

```
kubectl get namespace $NAMESPACE -o json > $NAMESPACE.json
curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" \
  -H "Content-Type: application/json" \
  -X PUT -k \
  --data-binary @$NAMESPACE.json \
  https://127.0.0.1:6443/api/v1/namespaces/$NAMESPACE/finalize
```

```
kubectl get namespace rancher -o json > rancher.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @rancher.json https://127.0.0.1:6443/api/v1/namespaces/rancher/finalize
kubectl get namespace cattle-system -o json > cattle-system.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-system.json https://127.0.0.1:6443/api/v1/namespaces/cattle-system/finalize
kubectl get namespace cattle-fleet-system -o json > cattle-fleet-system.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-fleet-system.json https://127.0.0.1:6443/api/v1/namespaces/cattle-fleet-system/finalize
kubectl get namespace cattle-fleet-local-system -o json > cattle-fleet-local-system.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-fleet-local-system.json https://127.0.0.1:6443/api/v1/namespaces/cattle-fleet-local-system/finalize
kubectl get namespace p-wmpdk -o json > p-wmpdk.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @p-wmpdk.json https://127.0.0.1:6443/api/v1/namespaces/p-wmpdk/finalize
kubectl get namespace p-nnsqm -o json > p-nnsqm.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @p-nnsqm.json https://127.0.0.1:6443/api/v1/namespaces/p-nnsqm/finalize
kubectl get namespace cattle-impersonation-system -o json > cattle-impersonation-system.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-impersonation-system.json https://127.0.0.1:6443/api/v1/namespaces/cattle-impersonation-system/finalize
kubectl get namespace cattle-global-data -o json > cattle-global-data.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-global-data.json https://127.0.0.1:6443/api/v1/namespaces/cattle-global-data/finalize
kubectl get namespace cattle-global-nt -o json > cattle-global-nt.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-global-nt.json https://127.0.0.1:6443/api/v1/namespaces/cattle-global-nt/finalize
kubectl get namespace cattle-fleet-clusters-system -o json > cattle-fleet-clusters-system.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cattle-fleet-clusters-system.json https://127.0.0.1:6443/api/v1/namespaces/cattle-fleet-clusters-system/finalize
kubectl get namespace cluster-fleet-local-local-1a3d67d0a899 -o json > cluster-fleet-local-local-1a3d67d0a899.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @cluster-fleet-local-local-1a3d67d0a899.json https://127.0.0.1:6443/api/v1/namespaces/cluster-fleet-local-local-1a3d67d0a899/finalize
kubectl get namespace fleet-default -o json > fleet-default.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @fleet-default.json https://127.0.0.1:6443/api/v1/namespaces/fleet-default/finalize
kubectl get namespace fleet-local -o json > fleet-local.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @fleet-local.json https://127.0.0.1:6443/api/v1/namespaces/fleet-local/finalize
kubectl get namespace local -o json > local.json ; curl -k -H "Authorization: Bearer $JWT_TOKEN_KUBESYSTEM_DEFAULT" -H "Content-Type: application/json" -X PUT -k --data-binary @local.json https://127.0.0.1:6443/api/v1/namespaces/local/finalize
```

### force remove ! https://segmentfault.com/a/1190000040316179/en
```
KUBE_CONFIG=/etc/rancher/k3s/k3s.yaml
NS="cattle-system|*fleet*|rancher-operator-system|cattle-global-nt|cattle-global-data"
apt-get install jq -y
mv ../system-tools /usr/local/bin/
cat /etc/rancher/k3s/k3s.yaml > ~/.kube/config
bash ./remove_r_ha.sh
```
