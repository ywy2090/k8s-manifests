# k8s-manifests

所有服务 K8s 部署声明的唯一事实来源（GitOps）。Argo CD 监听本仓库并同步到 ACK 集群。

## 目录约定

```
services/<服务名>/
├── base/                  # 通用清单（deployment/service/serviceaccount）
└── overlays/<环境>/       # 环境差异：仅允许覆盖 tag、副本数、资源、环境变量
    └── kustomization.yaml
```

## 发布流程

1. Jenkins 构建并推送镜像后，自动更新 `services/<服务名>/overlays/<环境>/kustomization.yaml` 的 `newTag` 并 push
2. Argo CD 检测变化后同步到对应 ACK 命名空间
3. 生产环境：tag 变更走 MR，运维审批合并

## 规则

- 禁止直接 kubectl 修改集群（GitOps 漂移会被纠正）
- 禁止提交明文密钥
- 新服务复制 `services/acr-devmo` 作为模板
