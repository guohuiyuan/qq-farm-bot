<div align="center">

# QQ-FARM-BOT

QQ 农场全自动挂机管理平台 — 多账号、可视化、实时控制

[![license](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE) [![release](https://img.shields.io/badge/release-v2.0.0-green.svg)](package.json) [![node](https://img.shields.io/badge/node-%3E%3D16-brightgreen.svg)](https://nodejs.org) [![vue](https://img.shields.io/badge/vue-3.5-42b883.svg)](https://vuejs.org)

</div>

---

基于 Node.js + Vue 3 构建的 QQ 农场自动化工具，支持多账号同时管理，提供 Web 可视化面板，实现种植、收获、偷菜、任务领取、仓库出售等全流程自动化。

---

## 功能特性

### 农场自动化
- **自动收获** — 成熟作物即时收取
- **智能种植** — 根据经验/小时效率排名自动选择最优种子
- **自动施肥** — 种植后自动购买并施加肥料加速生长
- **自动除草 / 除虫 / 浇水** — 保持农场健康状态
- **自动任务** — 自动领取已完成的成长任务和每日任务奖励
- **自动出售** — 定时清理背包果实换取金币

### 好友系统
- **自动偷菜** — 智能检测好友成熟作物并偷取
- **帮好友浇水 / 除草 / 除虫** — 互助获取经验
- **智能预筛选** — 跳过无事可做的好友，减少无效请求

### 多用户权限
- **管理员 / 普通用户** 两级角色
- 管理员可管理所有账号，普通用户仅能操作被授权的 QQ 号
- JWT 认证，Session 数据 AES-256-CBC 加密存储

### 可视化面板
- **仪表盘** — 总览所有账号状态（运行中 / 停止 / 异常）
- **账号主页** — 等级、金币、经验、今日统计、功能开关实时切换
- **土地详情** — 每块地的植物、生长阶段、剩余时间
- **种植效率排行** — 根据等级动态计算作物经验/小时排名（含多季作物）
- **实时日志** — WebSocket 推送 Bot 运行日志
- **深色 / 浅色主题** 一键切换
- **移动端适配** — 手机也能正常使用

---

## 应用截图

| 账号管理 | 账号主页 |
|:---:|:---:|
| ![账号管理](docs/images/dashboard.png) | ![账号主页](docs/images/account-home.png) |

| 土地详情 | 设置 & 种植排行 |
|:---:|:---:|
| ![土地详情](docs/images/account-lands.png) | ![设置页](docs/images/account-settings.png) |

| 实时日志 | 用户管理 |
|:---:|:---:|
| ![日志页](docs/images/account-logs.png) | ![用户管理](docs/images/admin-users.png) |

---
## 技术栈

| 层 | 技术 |
|:---|:---|
| 后端 | Node.js + Express + Socket.io + WebSocket (ws) |
| 前端 | Vue 3 + Vite 6 + Element Plus + Vue Router + Pinia |
| 协议 | Protobuf (protobufjs) 编解码游戏消息 |
| 数据库 | SQLite (sql.js，纯 JS 无需 native 编译) |
| 认证 | 自实现 JWT (HMAC-SHA256) + SHA-256 密码哈希 |
| 加密 | AES-256-CBC 加密 Session 存储 |
| 实时通信 | Socket.io (前后端) + WebSocket (游戏服务器) |

---

## 快速开始 (源码运行)

### 环境要求

- **Node.js** >= 16

### 安装与启动

```bash
git clone [https://github.com/maile456/qq-farm-bot.git](https://github.com/maile456/qq-farm-bot.git)
cd qq-farm-bot

# 1. 一键安装所有依赖 (后端 + 前端)
npm run setup

# 2. 构建前端
npm run build:web

# 3. 启动服务
npm start

```

服务器默认运行在 `http://localhost:3000`。首次启动会自动创建默认管理员账号：`admin` / `admin123`（登录后请立即修改密码！）。

---

## Docker 部署 (推荐)

本项目提供了两种 Docker 部署方式，分别满足**小白用户快速体验**与**开发者本地调试**的需求。

### 1. 生产环境部署（面向普通用户）

直接拉取云端预编译好的镜像，实现秒级启动，无需在本地耗时编译环境。

1. 在你的服务器或本地新建一个目录，并下载 `docker-compose.yml` 文件。
2. （可选）修改文件中的 `BOT_ENCRYPT_KEY` 为你自己的 32 位随机字符串。
3. 在该目录下运行：

```bash
docker compose up -d

```

启动完成后，访问 `http://你的IP:3000` 即可。

### 2. 开发环境部署（面向开发者）

如果你修改了项目源码，希望在本地通过 Docker 重新构建并测试效果，请使用 `docker-compose.dev.yml`。

1. 克隆完整代码库到本地。
2. 在项目根目录下运行：

```bash
docker compose -f docker-compose.dev.yml up -d --build

```

> 首次本地构建需要 2~5 分钟时间（包含下载基础镜像、安装依赖和前端打包）。

---

## GitHub Actions 自动打包发布

本项目已配置完善的 GitHub Actions 工作流。当你推送代码并打上版本标签时，会自动构建跨平台镜像并推送到 DockerHub。

**如何配置你的专属构建流：**

1. Fork 本仓库。
2. 在你的仓库 **Settings** -> **Secrets and variables** -> **Actions** 中添加以下两个 Secret：
* `DOCKERHUB_USERNAME`: 你的 DockerHub 用户名
* `DOCKERHUB_TOKEN`: 你的 DockerHub 访问令牌 (Access Token)


3. 在 `docker-compose.yml` 中，将镜像地址修改为你自己的：`image: 你的用户名/qq-farm-bot:latest`
4. 提交代码后，打上 `v` 开头的 tag 并推送（例如 `git tag v2.0.0 && git push origin v2.0.0`），即可触发自动构建发布。

---

## 环境变量配置

| 变量 | 默认值 | 说明 |
| --- | --- | --- |
| `PORT` | `3000` | Web 面板与 API 服务端口 |
| `JWT_SECRET` | 内置随机值 | JWT 签名密钥（建议生产环境通过环境变量覆盖） |
| `BOT_ENCRYPT_KEY` | 内置默认值 | Session AES-256 加密密钥（强烈建议生产环境修改，需满 32 字符） |

---

## 致谢

本项目在学习和开发过程中参考了以下优秀的开源项目，在此表示感谢：

* [linguo2625469/qq-farm-bot](https://github.com/linguo2625469/qq-farm-bot) — QQ 农场 Bot 核心实现
* [lkeme/QRLib](https://github.com/lkeme/QRLib) — QQ 扫码登录库
* [QianChenJun/qq-farm-bot](https://github.com/QianChenJun/qq-farm-bot) — QQ 农场 Bot 参考实现
* [Penty-d/qq-farm-bot-ui](https://github.com/Penty-d/qq-farm-bot-ui) — QQ 农场 Bot 多功能参考实现

---

## 免责声明

本项目仅供学习和研究用途，请勿用于任何商业用途或违反服务条款的行为。使用本项目造成的任何后果由使用者自行承担。

---

## 许可证

[MIT License](https://www.google.com/search?q=LICENSE)