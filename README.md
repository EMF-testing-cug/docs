# docs
基础知识和操作文档（欢迎大家进行补充）


# 电磁场测量自动校准软件系统开发与协同指南

> 本项目面向电磁场测量设备校准的自动化控制与数据管理，采用“核心公共底座库（`calib_core`）+ 4 套独立业务前端”的架构体系。为保证 50 人团队协作高效、代码质量可控且互不冲突，所有成员必须严格遵守本规范，**严禁直接向 `main` 或 `develop` 分支推送代码**。

---

<details>
<summary><b>📌 一、 项目仓库与团队职责矩阵（点击展开）</b></summary>

<br>

系统由 6 个独立私有仓库组成，请严格按照各自分组在对应仓库下开展开发，严禁跨权限修改其他业务仓库：

| 仓库名称 | 仓库克隆地址 (HTTPS) | 职责范围与关键交付物 | 主责团队 |
| :--- | :--- | :--- | :--- |
| **`calib-core`** | `https://github.com/EMF-testing-cug/calib-core.git` | 通用仪器 SCPI 驱动、PaddleOCR 识别与三帧防抖、SQLite 存储、GUM 不确定度评定引擎及 CSV 导出 | 底层核心支撑组 |
| **`app-tem`** | `https://github.com/EMF-testing-cug/app-tem.git` | TEM 室 10kHz~300MHz 频段各向同性校准、场强幅值与频响测试业务前端 | TEM 室开发组 |
| **`app-gtem`** | `https://github.com/EMF-testing-cug/app-gtem.git` | µTEM/GTEM 室 10MHz~1GHz 参考场强计定标、校准文件生成与被检表校准前端 | µTEM/GTEM 组 |
| **`app-chamber`** | `https://github.com/EMF-testing-cug/app-chamber.git` | 暗室 1GHz~18GHz 射频校准前端，控制 1m 轴线对准、双功放频段路由及各向同性步进 | 暗室射频开发组 |
| **`app-magnetic`** | `https://github.com/EMF-testing-cug/app-magnetic.git` | 亥姆赫兹线圈工频磁场线性度与频响校准前端，实现背景磁场扣除与 A/m、µT 换算 | 工频磁场开发组 |
| **`calib-integration`** | `https://github.com/EMF-testing-cug/calib-integration.git` | 虚拟仪器（Mock）服务、多系统联调测试用例、PyInstaller 独立离线安装包打包工程 | 总装集成组 |

</details>

---

<details>
<summary><b>⚙️ 二、 首次环境初始化（仅需执行一次）</b></summary>

<br>

### 1. 配置 Git 基础身份

打开本地终端（PowerShell 或 Git Bash），配置统一的姓名与邮箱（与 GitHub 注册信息保持一致）：

```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
