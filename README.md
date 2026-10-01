# k8s-deploy

Kubernetes 部署清单集合：**Apollo 四环境部署** + **WordPress（Kustomize）**。

## Apollo（命名空间 `sre`）

由 Apollo 官方 Kubernetes 示例改造而来，拆成 **dev / fat / uat / prod 四套环境**，每套三份清单：

| 文件 | 作用 |
| --- | --- |
| `service-mysql-for-apollo-<env>-env.yaml` | 该环境的 MySQL（ApolloConfigDB） |
| `service-apollo-config-server-<env>.yaml` | 该环境的 Config Service |
| `service-apollo-admin-server-<env>.yaml` | 该环境的 Admin Service |
| `service-apollo-portal-server.yaml` | Portal（各环境共用一套） |

一键部署：

```bash
cd apollo
kubectl create namespace sre
bash kubectl-apply.sh          # 依次 apply dev → fat → uat → prod
```

部署前需要改的（详细步骤见 [apollo/README.md](apollo/README.md)）：

1. 各环境 Config / Admin 清单里 `application-github.properties` 的 `spring.datasource.url` / `username` / `password`
2. 各环境 MySQL 清单里的 endpoint 地址

> `apollo/` 下的清单与脚本来自 **Apollo 官方示例**（文件头保留 Apache-2.0 版权声明），改造点是拆出四套环境；`apollo/LICENSE` 为上游项目的许可证。

## WordPress（Kustomize）

```bash
kubectl apply -k wordpress
```

`kustomization.yaml` 用 `secretGenerator` 生成 `mysql-pass`，密码**硬编码在文件里**（`abc123`），仅供本地演示，**不要用于任何真实环境**。

## 说明

- 所有清单面向本地 / 演示集群，未做资源配额、健康探针、高可用等生产加固
- 镜像版本与 StorageClass 请按自己的集群调整
