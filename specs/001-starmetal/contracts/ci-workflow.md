# ci-workflow contract

- Name: Build & Push starmetal images
- Triggers:
  - `push`: branches [`starmetal`]
  - `workflow_dispatch`:
    - inputs.tag (string, optional) — 自定义额外标签，留空仅推送 `latest` + `sha`
- Secrets (required):
  - `DOCKERHUB_USERNAME`
  - `DOCKERHUB_TOKEN` (Docker Hub Access Token)
- Matrix targets (two images):
  - `service`: context `.`, dockerfile `docker/wvp/Dockerfile`, image `starmetal/wvp-service`
  - `web`: context `.`, dockerfile `docker/nginx/Dockerfile`, image `starmetal/wvp-web`
- Tags per image:
  - `latest`
  - `${{ github.sha }}`
  - `${{ inputs.tag }}` (仅当手动触发且提供)
- Jobs:
  - `build-and-push` (runs-on `ubuntu-latest`)
    - Checkout
    - Setup buildx
    - Login Docker Hub with secrets
    - Build & push via `docker/build-push-action@v5` using matrix vars
- Outputs:
  - 镜像已推送到 Docker Hub（两镜像各最多三个标签：latest、sha、可选自定义）
- Failure conditions:
  - 缺少必需 secrets
  - Dockerfile 构建失败
  - 无网络或 Docker Hub 认证失败

