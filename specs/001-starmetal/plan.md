# Implementation Plan: GitHub Actions 发布 starmetal 镜像

**Branch**: `starmetal` | **Date**: 2025-12-06 | **Spec**: 用户口头需求（无 spec.md）
**Input**: 需求：新增 GitHub workflow，参考 `docker/docker-compose.yml` 构建 `docker/wvp/Dockerfile` 与 `docker/nginx/Dockerfile`，并以 `starmetal/wvp-service`、`starmetal/wvp-web` tag 推送 Docker Hub。

## Summary

为仓库添加 CI 工作流（`.github/workflows/starmetal-build.yml`），自动构建后端服务镜像（wvp/Dockerfile）与前端静态资源镜像（nginx/Dockerfile），命名为 `starmetal/wvp-service` 与 `starmetal/wvp-web`，输出双标签（latest + git sha/自定义），并推送到 Docker Hub。

## Technical Context

**Language/Version**: GitHub Actions YAML；Java 11 + Maven；Node/npm（Ubuntu 24.04 默认）  
**Primary Dependencies**: Docker buildx、docker/login-action、docker/build-push-action、Maven、npm  
**Storage**: N/A  
**Testing**: 构建阶段不跑测试（与现有 Dockerfile 一致）  
**Target Platform**: GitHub Actions `ubuntu-latest` runner，发布到 Docker Hub  
**Project Type**: CI/CD 工作流（后端 + 前端镜像）  
**Performance Goals**: 单次 workflow 构建并推送两镜像；可选缓存  
**Constraints**: 需访问 Docker Hub；需 secrets `DOCKERHUB_USERNAME`、`DOCKERHUB_TOKEN`  
**Scale/Scope**: 2 镜像：`starmetal/wvp-service`、`starmetal/wvp-web`（tag: latest + sha/自定义）

## Constitution Check

- `.specify/memory/constitution.md` 为空模板，未定义强制门禁；当前无阻碍。后续如补充宪章需重新校验。

## Project Structure

### Documentation (this feature)

```text
specs/001-starmetal/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
└── contracts/
```

### Source Code (repository root)

```text
.github/workflows/        # 新增 workflow 目标路径
docker/
├── docker-compose.yml    # 参考镜像/端口/环境
├── wvp/Dockerfile        # Java 11 + Maven 打包后端
└── nginx/Dockerfile      # Node/npm 构建静态资源 + nginx 基础镜像
wvp/                      # 后端源码（Maven）
web/                      # 前端源码
```

**Structure Decision**: 单仓库 CI/CD 工作流，直接在现有 Dockerfile 基础构建两镜像并推送。

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| N/A | 当前无超出基线的复杂度 | N/A |
