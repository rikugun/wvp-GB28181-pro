# quickstart

1) 准备机密  
   - 在 GitHub 仓库 Settings → Secrets → Actions 添加 `DOCKERHUB_USERNAME`、`DOCKERHUB_TOKEN`（Docker Hub Access Token）。  
   - 确认 Docker Hub 上已存在或可自动创建 `starmetal/wvp-service`、`starmetal/wvp-web` 仓库。

2) 触发方式  
   - push 到分支 `starmetal` 自动构建。  
   - 或在 Actions 中手动选择 `Build and Push starmetal images` (`.github/workflows/starmetal-build.yml`)，`Run workflow` 时可输入可选 tag（为空则仅推 `latest` + `sha`）。

3) 产出  
   - `starmetal/wvp-service:latest` 与 `starmetal/wvp-service:${{ github.sha }}`/输入 tag。  
   - `starmetal/wvp-web:latest` 与 `starmetal/wvp-web:${{ github.sha }}`/输入 tag。

4) 验证  
   - 在 Docker Hub 检查新标签；或本地/服务器执行 `docker pull starmetal/wvp-service:latest` 与 `docker pull starmetal/wvp-web:latest`。  
   - 若需引用特定构建，使用 sha/自定义标签。

