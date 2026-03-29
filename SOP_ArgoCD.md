SOP_ArgoCD.md (GitOps 部署与排障)

### [cite_start]1. 基础设施与代理配置 [cite: 164, 171]
[cite_start]由于国内环境拉取镜像易报 403 错误，必须配置 K3s 运行时代理 [cite: 172]：
* [cite_start]**代理设置**: 在 `/etc/systemd/system/k3s.service.d/http-proxy.conf` 中配置 `HTTP_PROXY` 指向宿主机代理地址 [cite: 175-182]。
* [cite_start]**生效命令**: 执行 `systemctl daemon-reload` 并重启 `k3s` 服务 [cite: 185, 186]。

### [cite_start]2. GitLab 仓库对接 (安全规范) [cite: 187]
* [cite_start]**身份验证**: 使用 **Personal Access Token** 而非登录密码 [cite: 188, 194]。
* [cite_start]**权限范围**: 勾选 `api` 或 `read_repository` [cite: 189]。
* [cite_start]**连接配置**: 在 ArgoCD 中勾选 `Skip TLS Verify` 并粘贴 Token [cite: 195]。

### [cite_start]3. GitOps 黄金定律 [cite: 201]
* [cite_start]**唯一可信源**: 所有配置修改必须在 GitLab 进行 [cite: 202]。
* [cite_start]**自愈机制**: 若手动通过 `kubectl` 修改副本数，会被 ArgoCD 的自愈功能强制回滚 [cite: 203]。
* [cite_start]**拉取策略**: 设置 `imagePullPolicy: IfNotPresent` 以优先使用本地镜像，避免重复拉取 [cite: 198]。

### [cite_start]4. 常用诊断指令集 [cite: 204]
| 需求 | 命令 |
| :--- | :--- |
| 查看 Pod 死亡原因 | [cite_start]`kubectl describe pod <name> -n <ns>` [cite: 205] |
| 查看 K3s 内部镜像 | [cite_start]`sudo k3s crictl images` [cite: 205] |
| 测试代理连通性 | [cite_start]`curl -I -x http://<IP>:7890 https://google.com` [cite: 205] |
| 本地临时访问应用 | [cite_start]`kubectl port-forward svc/<name> <port>:<port> --address 0.0.0.0` [cite: 205] |
