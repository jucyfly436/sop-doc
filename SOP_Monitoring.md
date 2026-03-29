SOP_Monitoring.md (监控体系搭建)

### [cite_start]1. 核心架构组成 [cite: 39, 40]
[cite_start]本方案通过 `docker-compose.yml` 编排了 5 个核心组件，实现全方位监控 [cite: 41]：
* [cite_start]**Prometheus**: 监控的“大脑”，负责存储和查询时序数据 [cite: 42]。
* [cite_start]**Grafana**: 监控的“眼睛”，负责数据可视化 [cite: 43]。
* [cite_start]**Node-Exporter**: 采集 Linux 宿主机硬件信息（CPU、内存等） [cite: 44]。
* [cite_start]**cAdvisor**: 采集 Docker 容器资源使用情况 [cite: 45]。
* [cite_start]**Redis-Exporter**: 采集 Redis 内部指标（命中率、连接数等） [cite: 46]。

### [cite_start]2. 关键配置要点 [cite: 47]
* [cite_start]**Prometheus 采集任务**: 必须在 `prometheus.yml` 中定义所有 Job 目标，例如 `node-exporter:9100` 和 `redis-exporter:9121` [cite: 48, 54-62]。
* [cite_start]**持久化与安全**: Redis 必须挂载 volumes 并开启 `appendonly yes` 以确保数据关机不丢失 [cite: 67, 81-84]。
* [cite_start]**架构对齐**: 对于 x86_64 机器，必须确保镜像版本正确，避免 ARM 架构镜像导致的 `Exec Format Error` [cite: 65, 88]。

### [cite_start]3. Grafana 仪表盘推荐 [cite: 76, 77]
导入以下 ID 即可快速获得专业监控面板：
* [cite_start]**1860**: 宿主机监控 (Node Exporter) [cite: 78]。
* [cite_start]**14282**: 容器监控 (cAdvisor) [cite: 79]。
* [cite_start]**763**: Redis 深度监控 [cite: 80]。
