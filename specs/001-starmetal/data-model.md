# data-model

## Entities

- DockerImage  
  - name: `starmetal/wvp-service` | `starmetal/wvp-web`  
  - context: `.`  
  - dockerfile: `docker/wvp/Dockerfile` | `docker/nginx/Dockerfile`  
  - tags: `latest`, `${{ github.sha }}`，`inputs.tag`（可选）  
  - registry: Docker Hub (`docker.io`)  
  - build_args: 无（当前 Dockerfile 不需要）

- WorkflowRun  
  - trigger: `push`(branch=`starmetal`) 或 `workflow_dispatch`(tag 输入)  
  - env: `DOCKERHUB_USERNAME`、`DOCKERHUB_TOKEN`（必需）  
  - outputs: 镜像 digest/标签  
  - jobs: `build-and-push`（matrix 两镜像）

## Relationships

- WorkflowRun → DockerImage（1:N）：一次运行构建并推送两种镜像（service/web）。

