# Kubernetes 的技術問題排解技巧

1. 常常遇到無法刪除資源的狀況可以這樣做

```
kubectl patch crd/functions.kubeless.io -p '{"metadata":{"finalizers":[]}}' --type=merge
```

> 各種資源都可以只要替換你想要刪除的資源名稱即可

> Apr 12th, 2020 15:10:00pm