# Tasks: GitHub Actions 发布 starmetal 镜像

**Input**: `specs/001-starmetal/{plan.md,research.md,data-model.md,contracts/ci-workflow.md,quickstart.md}`  
**Prerequisites**: plan/spec 基于口头需求（无正式 spec.md），单一用户故事（构建并推送两镜像）。

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: 准备工作流目录与文件骨架。

- [ ] T001 确认工作流目录存在 `.github/workflows/`
- [ ] T002 创建工作流文件骨架 `.github/workflows/starmetal-build.yml`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: 保障镜像推送所需的认证与基础信息。

- [ ] T003 记录/校验必需 secrets（`DOCKERHUB_USERNAME`、`DOCKERHUB_TOKEN`）在仓库设置中；在 `specs/001-starmetal/quickstart.md` 补充已校验说明

**Checkpoint**: secrets 明确后方可实施工作流。

---

## Phase 3: User Story 1 - 自动构建并推送 starmetal 镜像 (Priority: P1) 🎯 MVP

**Goal**: 自动构建 `starmetal/wvp-service` 与 `starmetal/wvp-web` 镜像并推送 Docker Hub，支持 push/dispatch 触发与自定义标签。

**Independent Test**: 在 Actions 手动触发 workflow（或 push 到 `starmetal`）后，Docker Hub 出现最新的 `latest` 与 `sha`/自定义标签；构建日志显示两镜像均成功推送。

### Implementation for User Story 1

- [ ] T004 [US1] 定义触发条件（push 分支 `starmetal` + `workflow_dispatch` 输入 tag）于 `.github/workflows/starmetal-build.yml`
- [ ] T005 [US1] 配置 Docker Hub 登录步骤（`docker/login-action`）于 `.github/workflows/starmetal-build.yml`
- [ ] T006 [US1] 配置 buildx 环境与矩阵（service/web，context `.`，Dockerfile 分别 `docker/wvp/Dockerfile`、`docker/nginx/Dockerfile`）于 `.github/workflows/starmetal-build.yml`
- [ ] T007 [US1] 为矩阵镜像添加多标签（`latest`、`${{ github.sha }}`、可选 `inputs.tag`）并使用 `docker/build-push-action@v5` 推送至 Docker Hub 于 `.github/workflows/starmetal-build.yml`
- [ ] T008 [US1] 在 `specs/001-starmetal/quickstart.md` 补充 workflow 名称、手动触发示例与拉取验证步骤

**Checkpoint**: Workflow 可触发且两镜像成功推送。

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: 文档与可靠性提升。

- [ ] T009 审查 workflow 步骤日志与失败重试策略，必要时补充 `retry`/`timeout-minutes` 注释于 `.github/workflows/starmetal-build.yml`
- [ ] T010 在 `specs/001-starmetal/plan.md` 记录已创建的 workflow 文件名与触发策略
- [ ] T011 复核 `specs/001-starmetal/contracts/ci-workflow.md` 与实际 workflow 同步更新标签/矩阵细节

---

## Dependencies & Execution Order

- Phase 1 → Phase 2 → Phase 3 (US1) → Polish。  
- US1 任务在工作流文件内有顺序依赖：T004 → T005/T006 → T007；T008 可在 T004 后进行。  
- Polish 任务依赖 US1 完成。

---

## Parallel Opportunities

- T003（secrets 校验记录）可与 T004 之后的文档类任务并行，但工作流文件修改建议单线程避免冲突。  
- 如有多人协作，可一人负责 workflow YAML（T004-T007），另一人负责文档更新（T003, T008, T010, T011）。

---

## Implementation Strategy

- MVP：完成 US1（T004-T007）并确认镜像成功推送。  
- 若出现构建时间过长，可后续在 Polish 阶段添加缓存/并发优化。

