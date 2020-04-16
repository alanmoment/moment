# 使用 Kops 建立 Kubernetes

想要自己管理 Kubernetes 的節點，讓可控制的事項更多，也不想使用 Manager Service，可選擇的工具之一是 Kops ，這是官方推薦的管理工具，我覺得這種方式也是蠻方便的。建立的步驟雖然不多，但是管理上其實也不容易。

而 Github 上的[教學頁面](https://github.com/kubernetes/kops/blob/master/docs/cli/kops_create_cluster.md)，其實蠻清楚的，就不贅述。因為我是建立在 AWS 之上，所以還另外操作[建立 AWS 資源](https://github.com/kubernetes/kops/blob/master/docs/getting_started/aws.md)。

其中如果想建立在已存在的 VPC 之上，可以在 `kops create cluster` 的參數之中加入 `--vpc`，若沒有指定 VPC 則 Kops 會自動幫忙建立。

## 推薦用法

在凡事 IaC 的世代，執行任何操作，我都盡可能地留下文件，因為自己也挺健忘的，這可以幫助記憶。所以我不會直接用指令建立 cluster 而是選擇這樣。

```
kops create cluster --name=kubernetes-cluster.example.com \
  --state=s3://kops-state-1234 \
  --zones=eu-west-1a \
  --node-count=2 \
  --dry-run \
  -oyaml > filename.yaml
```

先產生 yaml file 再透過檔案執行 `kops create -f filename.yaml`，這樣只要在刪掉 cluster 之後還想要重建的話，就很方便了。

在建立 cluster 之後，還需要建立 AWS 上的資源，我也不推薦直接執行 `kops update cluster`，而是選擇下列用法

```
kops update cluster --out=terraform/ --target=terraform
```

如此一來，我可以非常清楚的知道 kops 會在 AWS 上幫我建立什麼資源。我覺得非常棒！當然以上只是非常基本的起手式，後面還有更多的挑戰。

> Apr 12th, 2020 14:40:00pm