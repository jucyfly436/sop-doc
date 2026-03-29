

> [cite_start]**核心目标**：从零构建高可用 K3s 集群，解决国内环境下的镜像拉取与存储冲突问题 [cite: 104]。

## 一、 基础设施与集群初始化
### 1. OS 环境修复
* [cite_start]**CentOS 7 EOL 修复**：替换官方停服源为阿里云归档源（vault.centos.org），执行 `yum clean all && yum makecache` [cite: 106]。
* [cite_start]**纠正克隆机网络**：清理 `/etc/hosts` 中残留的无效 IP（如 192.168.6.x），统一内网 DNS 解析为真实的 192.168.21.x 网段 [cite: 107]。

### 2. K3s 集群组建
* [cite_start]**Master 节点**：利用 Rancher 国内镜像源一键安装 [cite: 109]。
* [cite_start]**身份凭证**：通过 `cat /var/lib/rancher/k3s/server/node-token` 提取 Token [cite: 110]。
* [cite_start]**Worker 节点加入**：通过 `K3S_URL` 和 `K3S_TOKEN` 环境变量完成 3 节点分布式物理集群合体 [cite: 111]。

---

## 二、 镜像网络攻坚 (解决 403 Forbidden)
### 1. 现象与根因
* [cite_start]**现象**：Pod 长时间卡在 `ContainerCreating` [cite: 115]。
* [cite_start]**根因**：国内无法直连 Docker Hub，且 K8s 强依赖底层容器引擎的命名空间隔离 [cite: 116]。

### 2. 对策：手动搬运法
1. [cite_start]使用 `k3s ctr image pull` 从国内代购源拉取 `pause` 和 `redis` 镜像 [cite: 118]。
2. [cite_start]**关键步骤**：使用 `k3s ctr -n k8s.io image tag` 将镜像强行注入 K8s 专属命名空间并贴上官方标签 [cite: 119]。
3. [cite_start]执行 `kubectl delete pod --all` 强制刷新本地缓存 [cite: 120]。

---

## 三、 持久化存储与流量控制
### 1. NFS 动态挂载
* [cite_start]**服务端配置**：确保 `/etc/exports` 权限设置为 `(rw,sync,no_root_squash)`，否则容器无法写入 [cite: 128]。
* [cite_start]**排雷**：若 Deployment 使用多副本挂载同一 NFS 路径，会导致 Redis 多个进程同时操作一个 `.rdb` 文件，建议使用单副本或 StatefulSet [cite: 131]。

### 2. 七层流量 (Ingress)
* [cite_start]**控制器**：使用 K3s 自带的 Traefik [cite: 134]。
* [cite_start]**域名访问**：通过 `spec.rules.host` 定义域名（如 `k8s.test.com`） [cite: 136]。
* [cite_start]**端口监听**：必须添加注解 `traefik.ingress.kubernetes.io/router.entrypoints: web` 确保监听 80 端口 [cite: 137]。

---

## 四、 核心排障工具链 (Troubleshooting)
| 现象 | 根因 | 对策 |
| :--- | :--- | :--- |
| **i/o timeout** 连不上 API Server | [cite_start]内核转发关闭或防火墙拦截 [cite: 139, 140] | [cite_start]`systemctl stop firewalld` 并开启 `net.ipv4.ip_forward=1` [cite: 142, 143] |
| **HPA 副本数异常** | [cite_start]HPA 与手动修改冲突 [cite: 147] | [cite_start]HPA 会强制重置副本数，手动修改 YAML 无效 [cite: 148] |
| **Ingress 无法访问** | [cite_start]`ADDRESS` 为空 [cite: 152] | [cite_start]检查 `IngressClass` 是否匹配 [cite: 152] |

---

## 五、 自动化交付 (Helm)
* [cite_start]**架构思路**：将 Redis、Whoami 和 Ingress 的 YAML 全部放入 `templates/` 目录 [cite: 155]。
* [cite_start]**参数化**：将 NFS IP、域名、镜像版本抽离到 `values.yaml`，使用 `{{ .Values.xxx }}` 引用 [cite: 157, 158]。
* [cite_start]**环境隔离**：使用 `{{ .Release.Name }}` 实现多套环境共存 [cite: 159]。
