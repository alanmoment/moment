# 如何開始使用 Helm

有鑒於目前支援 Helm V3 的人還不是太多，我自己本身也還在使用 Helm V2，所以就先記錄一下這個版本的操作

## 初始化

Helm V2 需要在 Server 及 Client 都安裝套件，Client 還比較好解決，想要使用 `brew` 安裝也可以，想要自行下載 binary 的檔案也可以。

### 從 brew

```
brew install helm
```

### 自行下載

Download: https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-darwin-amd64.tar.gz

```
# tar zxvf helm-v2.13.1-darwin-amd64.tar.gz
# mv darwin-amd64/helm /usr/local/bin/helm
```

而 Server 的操作，我會這樣做避免遇到權限不足的情況。

先建立 `helm-tiller.yaml`

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

再建立資源

```
kubectl apply -f helm-tiller.yaml
```

最後在 init 的時候帶入參數

```
helm init --service-account tiller --upgrade 
```

而其實我避免 tiller 安裝到可能會被回收的 node 之上，我會放在固定不會回收的 node。再加上避免過多次的反覆刪除、安裝，會有大量不需要的歷史紀錄塞爆 etcd，我會這樣做。

```
helm init --service-account tiller --node-selectors "purpose=kube-service" --tiller-namespace kube-system --upgrade --override 'spec.template.spec.tolerations[0].key=CriticalAddonsOnly,spec.template.spec.tolerations[0].operator=Exists,spec.template.spec.tolerations[0].effect=NoSchedule' --history-max 30
```

如此一來 tiller 會被我安裝到具有 `purpose=kube-service` Tag 的 node，也被限制歷史紀錄不會超過 30 次

## 基本操作及一些小技巧

在寫完 Helm chart 之後，安裝前，我都會先執行 `helm template` 瀏覽一下之後會被套用在 Kubernetes 的 yaml config 長怎麼樣。

```
helm template --name <release-name> --output-dir ./output ./
```

因應自動化，避免繁複的判斷邏輯，安裝時，可以這樣做，避免已經安裝的 helm chart 不能被安裝

```
helm upgrade --install <release-name> <repo>/<release-name>
```

> Apr 12th, 2020 16:13:00pm