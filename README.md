# sop-doc
# Cloud-Native-SOP-Labs
> 这是一个关于 Go 开发、容器化编排、监控体系及 K8s 运维的全栈技术 SOP 仓库。

##  目录指南
本仓库包含以下核心阶段的标准化作业程序：

1.  [Go 容器化开发全流程](./SOP_Go_Docker.md)
     [cite_start]涵盖宿主机内核转发配置 [cite: 4, 5, 6][cite_start]、Air 热更新集成 [cite: 10, 12, 35] [cite_start]及 Redis AOF 持久化方案 [cite: 18, 37]。
2. [Prometheus + Grafana 监控体系](./SOP_Monitoring.md)
   [cite_start]包含 Node-Exporter、cAdvisor 等 5 大组件编排 [cite: 41-46] [cite_start]与 Grafana 仪表盘导入流程 [cite: 76-80]。
3. [K8s & ArgoCD 进阶实战](./SOP_K8s_ArgoCD.md)
   [cite_start]**集群治理**：K3s 多节点组建 [cite: 108-111] [cite_start]与国内镜像拉取代理配置 [cite: 171-186]。
   [cite_start]**GitOps**：GitLab 仓库 Token 对接 [cite: 187-195] [cite_start]与 ArgoCD 自愈策略 [cite: 200, 203]。
   [cite_start]**服务网格**：Istio Minimal 模式安装及内存优化建议 [cite: 213, 215]。

## 核心技术栈
语言: Go 1.25+ (Alpine) [cite: 9]
容器: Docker / Docker Compose / K3s [cite: 13, 102, 163]
监控: Prometheus / Grafana / Redis-Exporter [cite: 42, 43, 46]
交付: Helm / ArgoCD / Istio [cite: 153, 163, 206]

## Checkpoint
网络：若出现 i/o timeout，优先检查 `net.ipv4.ip_forward` 是否为 1 [cite: 139-143]。
存储：Redis 多副本挂载 NFS 易冲突，建议使用单副本或 StatefulSet [cite: 131]。
镜像：国内环境拉取失败时，需手动通过 `k3s ctr` 注入 `k8s.io` 命名空间 [cite: 118, 119]。

---
#持续更新
