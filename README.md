本仓库记录了从零构建 Go 开发环境、监控体系到 K8s/GitOps 自动化运维的标准化作业程序（SOP），旨在解决国内网络环境下云原生工具链的落地痛点。

## 🚀 三阶段进阶路径

### 1\. [Phase 1: Go + Redis 容器化开发](https://www.google.com/search?q=./SOP_Go_Docker.md)

  * [cite_start]**核心配置**：宿主机内核转发 `net.ipv4.ip_forward = 1` [cite: 5-6]。
  * [cite_start]**开发工具**：集成 `Air` 实现 Go 代码热更新 [cite: 10, 12, 35]。
  * [cite_start]**数据安全**：利用 Docker Volume 挂载与 Redis AOF 开启（`--appendonly yes`）确保数据持久化 [cite: 17-18, 37]。

### 2\. [Phase 2: Prometheus + Grafana 监控体系](https://www.google.com/search?q=./SOP_Monitoring.md)

  * [cite_start]**全方位采集**：包含 Node-Exporter、cAdvisor 及 Redis-Exporter 等 5 大核心组件 [cite: 41-46]。
  * [cite_start]**可视化方案**：集成 Grafana 仪表盘（ID: 1860, 14282, 763） [cite: 78-80]。
  * [cite_start]**排障验证**：通过写入测试验证 “外网 -\> 内网 -\> 容器” 的数据连通性 [cite: 98-101]。

### 3\. [Phase 3: K3s + ArgoCD + Istio 进阶实战](https://www.google.com/search?q=./SOP_K8s_Advanced.md)

  * [cite_start]**集群治理**：解决 CentOS 7 EOL 换源问题及多节点 K3s 组网 [cite: 106, 111]。
  * [cite_start]**镜像攻坚**：针对 403 Forbidden 错误，采用手动 `k3s ctr` 注入 `k8s.io` 命名空间或配置系统级代理 [cite: 118-119, 172-182]。
  * [cite_start]**自动化交付**：基于 GitLab Token 的 ArgoCD 绑定与 Helm 参数化部署 [cite: 154-158, 187-195]。
  * [cite_start]**服务网格**：Istio Minimal 模式安装，优化内存开销 [cite: 215]。

## 🛠️ 常用运维工具箱 (Toolkit)

| 需求 | 核心指令 |
| :--- | :--- |
| **容器热更新** | [cite_start]`docker compose up --build -d` [cite: 29] |
| **Pod 故障定位** | [cite_start]`kubectl describe pod <name> -n <ns>` [cite: 205] |
| **镜像列表查询** | [cite_start]`sudo k3s crictl images` [cite: 205] |
| **网络代理测试** | [cite_start]`curl -I -x http://<Proxy_IP>:7890 https://google.com` [cite: 205] |

## ⚠️ 避坑检查点 (Checkpoints)

  * [cite_start]**网络连通性**：若 Ingress 或 API Server 超时，检查 `firewalld` 是否关闭及网桥流量是否打通 [cite: 140-144]。
  * [cite_start]**GitOps 准则**：严禁手动修改集群副本数，所有配置变更必须通过 GitLab 触发 ArgoCD 自愈 [cite: 202-203]。
  * [cite_start]**资源限制**：内存不足 2G 时，Istio 必须采用 Minimal 模式防止 OOM [cite: 209, 215]。
