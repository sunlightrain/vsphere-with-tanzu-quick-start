# vSphere with Tanzu 快速入门演示

## 目的

本仓库提供一个针对 vSphere with Tanzu 的最小化快速入门示例，演示如何：

- 登录 Supervisor 集群
- 创建一个 Tanzu Kubernetes (TKG) 工作负载集群
- 申请存储（PersistentVolume / PVC）
- 部署示例 Pod 并通过 Service 暴露

## 前置条件

- 已部署并配置好的 vSphere with Tanzu（Supervisor 集群）
- 已安装 `kubectl` 及 vSphere 的 `kubectl` 插件（Namespace CLI 工具）
- 能访问 vCenter / Supervisor 集群所需的网络并在对应 Namespace 拥有 RBAC 权限
- 推荐在 Linux 或 macOS Shell 下执行（Windows 用户需将命令转换为等价的 PowerShell 命令）

## 快速上手（概要）

1. 导出环境变量并登录 Supervisor 集群：

```sh
export SC_IP=10.198.52.128
export NAMESPACE=myles
kubectl vsphere login --server=https://$SC_IP --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify
```

2. 切换到 Namespace 上下文（Supervisor cluster context）：

```sh
kubectl config use-context $NAMESPACE
```

3. 创建一个 Tanzu 工作负载集群（TKC）：

```sh
kubectl apply -f manifests/tkc.yaml
```

4. 当 TKC 就绪后，登录并切换到 TKG 集群：

```sh
kubectl vsphere login --server=$SC_IP --tanzu-kubernetes-cluster-name tkc-1 --tanzu-kubernetes-cluster-namespace $NAMESPACE --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify
kubectl config use-context tkc-1
```

5. 部署示例存储和工作负载（位于 `manifests/` 目录）：

```sh
kubectl apply -f manifests/pvc.yaml
kubectl apply -f manifests/pod.yaml
kubectl apply -f manifests/svc.yaml
```

下文包含详细步骤、故障排查提示与示例命令。

## 登录 Supervisor 集群

（原文详细说明保留：如何获取 Namespace CLI 工具、在 vCenter UI 中找到“Link to CLI Tools”，以及截图参考。）

### 使用 `kubectl` 登录

示例：导出 Supervisor IP (`SC_IP`) 与 `NAMESPACE`，并通过 vSphere 插件登录。你会被提示输入密码，并且需要确保你的用户在该 Namespace 有足够权限。

```sh
export SC_IP=10.198.52.128
export NAMESPACE=myles
kubectl vsphere login --server=https://$SC_IP --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify
```

### 切换到 Supervisor 上下文

```sh
kubectl config use-context $NAMESPACE
```

如果无法连接到期望上下文，可能是因为本地 `~/.kube/config` 已存在相同名称的上下文。此时可尝试：

```sh
kubectl config use-context $NAMESPACE-$SC_IP
```

## 部署 TKG Service 集群

使用仓库中的 `manifests/tkc.yaml` 向 Supervisor 请求创建一个 TKG 工作负载集群：

```sh
kubectl apply -f manifests/tkc.yaml
```

集群创建需要一些时间（通常约 30 分钟），过程会在 vCenter 中以 VM 的形式呈现。你可以通过 `kubectl describe tkc` 或 `kubectl get virtualmachine -w` 来观察状态。

## 登录到 TKG 工作负载集群

当 TKC 创建并就绪后，使用 vSphere 插件登录并切换上下文到 TKG 集群：

```sh
kubectl vsphere login --server=$SC_IP --tanzu-kubernetes-cluster-name tkc-1 --tanzu-kubernetes-cluster-namespace $NAMESPACE --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify
kubectl config use-context tkc-1
```

## 创建一个放宽的 Pod Security Policy（仅作 Quick Start 示范，不建议生产使用）

```sh
kubectl create clusterrolebinding default-tkg-admin-privileged-binding --clusterrole=psp:vmware-system-privileged --group=system:authenticated
```

## 部署示例工作负载

以下展示如何运行 `busybox` 以验证基础容器运行能力，以及如何使用仓库中的 YAML 清单部署带存储的 Nginx Pod 并通过 LoadBalancer 暴露。

示例：运行 `busybox` 并进入 shell：

```sh
kubectl run -i --tty busybox --image=quay.io/quay/busybox --restart=Never -- sh
```

示例：部署 PVC、Pod、Service：

```sh
kubectl apply -f manifests/pvc.yaml
kubectl apply -f manifests/pod.yaml
kubectl apply -f manifests/svc.yaml
```

你可以使用 `kubectl port-forward` 暂时访问 Pod，或创建 `Service`（`Type: LoadBalancer`）让 vSphere with Tanzu 分配外部 IP。详情请参照仓库中的具体注释与示例。

## 其他说明

- 仓库已添加 `manifests/kustomization.yaml`，可以通过 `kubectl apply -k manifests/` 一次性应用并注入演示命名空间 `tanzu-quickstart` 与公共标签。
- 建议后续加入 CI 校验（例如 `kubeconform`、`kube-linter`）以及 `LICENSE` / `CONTRIBUTING.md`。

---

如果需要，我可以把此中文 README 合并回 `README.md`（替换或作为中文副本），或提交到 PR（当前更改已在 `improve/readme-manifests` 分支）。
