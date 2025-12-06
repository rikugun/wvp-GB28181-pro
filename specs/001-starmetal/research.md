# research

- Decision: 使用 GitHub Actions 构建并推送两镜像，统一用 `docker/login-action` + `docker/build-push-action@v5`。  
  - Rationale: 官方维护、支持 buildx 和多 tag 推送，配置简单且缓存可选。  
  - Alternatives considered: 手写 `docker build && docker push`；放弃以减少脚本复杂度与错误处理成本。

- Decision: 镜像命名 `starmetal/wvp-service`（后端）与 `starmetal/wvp-web`（前端），双标签 `latest` + `sha`/`input`。  
  - Rationale: `latest` 便于默认拉取；`sha`/输入 tag 便于可追溯与回滚。  
  - Alternatives considered: 仅用 `latest`（不可追溯）；仅用分支名（无法定位具体提交）。

- Decision: 触发条件为 `push` 到 `starmetal` 分支 + `workflow_dispatch`（可输入自定义 tag）。  
  - Rationale: 需求指定使用当前分支；手动触发可用于补发镜像或自定义标签。  
  - Alternatives considered: 对所有分支生效，可能产生冗余推送。

- Decision: 复用现有 Dockerfile 路径和上下文：`docker/wvp/Dockerfile`（context `.`），`docker/nginx/Dockerfile`（context `.`）。  
  - Rationale: 与 `docker-compose.yml` 保持一致，避免路径/依赖漂移。  
  - Alternatives considered: 使用子目录为 context（需额外复制资源，不必要）。

- Decision: 准备所需机密 `DOCKERHUB_USERNAME`、`DOCKERHUB_TOKEN`。  
  - Rationale: 推送 Docker Hub 必需；分离用户名与 token 便于轮换。  
  - Alternatives considered: 使用 PAT 绑定到个人账户，不便共享和轮换。

