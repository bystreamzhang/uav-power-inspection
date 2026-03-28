# CLAUDE.md

## 项目概述

电力低空无人机智能巡检平台前端原型，客户演示用。目前为 Vue 3 CDN 单文件，后续迁移至 Vue 3 + Vite + FastAPI。

## 当前状态

- `index.html`：完整 8 页面原型，Vue 3 CDN，无构建工具，浏览器直开
- 所有数据为 mock，无后端
- 主题：深色系（可改浅色，见下方）

在vercel.com进行了部署，项目结构需要能让vercel识别。

`tech-stack.md` 记录了计划的技术栈。

docs文件夹有甲方的解决方案文档，包含项目所需信息；`docs/assets/` 存放原始图片（中文文件名）。

前端实际使用的图片已复制到 `assets/images/`（英文文件名，避免 URL 编码问题），路径引用为 `./assets/images/xxx.png`。对应关系：

- `hangar-mesh-network.png` ← 图12-智能机场自组网技术（机场页）
- `cloud-platform-overview.png` / `platform-capabilities.png` ← 图13/14（Dashboard）
- `hangar-access-flow.png` ← 图15-接入流程（绑定弹窗）
- `route-management.png` / `route-waypoint-planning.png` / `tower-inspection-paths.png` ← 图17/18/19（航线规划页）
- `defect-ai-algorithms.png` / `defect-component-detail.png` ← 图20/21（问题管理 AI tab）
- `report-template-example.png` ← 图22-巡检报告模板（报告页）

## 技术约定

### 前端

- **框架**：Vue 3 Composition API（`setup()`）
- **构建**（迁移后）：Vite + Vue Router 4 + Pinia
- **UI**：自定义 CSS 变量系统，不引入组件库，保持轻量
- **图表**：SVG 手写（原型）→ ECharts（正式版）
- **地图**：SVG 占位 → Leaflet（2D）/ Cesium（3D，后期）
- **HTTP**：axios，baseURL 指向 FastAPI

### 后端（FastAPI，待建）

- Python 3.11 + FastAPI + SQLAlchemy
- 路由前缀：`/api/v1/`
- 认证：JWT Bearer Token
- WebSocket：`/ws/drone/{drone_id}` 推送实时状态

### 关键 API 端点（规划）

```
GET  /api/v1/hangars          # 机场列表
GET  /api/v1/devices          # 设备列表
POST /api/v1/tasks            # 创建任务
GET  /api/v1/tasks/{id}       # 任务详情
GET  /api/v1/routes           # 航线列表
GET  /api/v1/defects          # 缺陷列表
POST /api/v1/reports/generate # 生成报告
GET  /api/v1/data/files       # 数据文件列表
WS   /ws/drone/{id}           # 无人机实时状态
```

## 页面与组件映射（迁移后）

```
views/
  Dashboard.vue       # 首页总览
  Hangar.vue          # 机场管理
  Device.vue          # 设备管理
  Task.vue            # 巡检任务
  Route.vue           # 航线规划
  Issue.vue           # 问题管理（含AI/人工/对比Tab）
  Report.vue          # 巡检报告
  DataAsset.vue       # 数据管理
  Settings.vue        # 系统设置
components/
  StatCard.vue        # 统计卡片
  DroneStatusCard.vue # 无人机状态小卡
  DefectCard.vue      # 缺陷卡片
  MapView.vue         # 地图组件（占位→Leaflet）
  modals/             # 各弹窗组件
```

## CSS 主题系统

所有颜色通过 `:root` CSS 变量控制：

- 改全局主题：修改 `:root` 中 `--bg/--bg2/--bg3/--text` 系列
- 侧边栏独立深色：`.sidebar { background: #0c1322 }` + `.sidebar .nav-item` 覆盖
- 主色调：`--primary` （当前 `#0ea5e9` 天蓝）

## Mock 数据位置

单文件版本中，所有 mock 数据在 `setup()` 函数内，变量名直观（`hangars`, `devices`, `tasks`, `defects` 等）。迁移后移至 `src/mock/` 目录。

## Token 节省原则（重要）

1. **优先阅读此文件和 README.md**，不要重复询问已知背景
2. **修改现有文件**用 str_replace，不要重写整个文件
3. **只改动被要求的部分**，不要"顺手优化"其他代码
4. **不需要解释已完成的工作**，直接给结果
5. **Mock 数据不需要真实性**，够演示即可，不要过度丰富
6. **CSS 改动**：优先改 `:root` 变量，而非逐条修改选择器

## 注意事项

- 目前无需实现真实功能，演示能点、能看、逻辑自洽即可
- 无人机型号参考：DJI M300-RTK、M4D、M4TD、Dock2/Dock3
- 线路名称参考：500kV鄂荆线、220kV武阳线、110kV江汉线
- 缺陷类型参考：绝缘子破损、导线磨损、设备发热、杆塔异物、防震锤偏移
