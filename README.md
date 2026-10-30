# ⚡ 电磁场测量自动校准软件系统开发与协同指南

> 欢迎加入实验室软件开发团队！工程开发与平时写简单脚本不同，我们 50 人需要协同打磨一套大型工业测控系统。
> 请大家**切勿慌张**，遇到不会的步骤直接点击下方对应的卡片展开查看；**只要严格遵循本流程，你的代码绝不会改崩主干，大胆尝试即可！**

---

<details>
<summary><b>🚨 【必读红线】新手团队三大纪律（点击展开）</b></summary>

1. **绝对禁止直接推送到 `main` 或 `develop` 分支**：所有人的代码都必须在自己的功能分支（`feature/xxx`）上写，然后发起合并申请（Pull Request）让组长审核。
2. **严禁在代码中写死本机绝对路径**：切勿写 `C:\Users\张三\Desktop\...` 或 `F:\研_doing\...`，必须使用相对路径，否则在其他同学电脑上必然报错闪退。
3. **驱动开发必须写 `mock_mode`**：由于实验室真实仪器数量有限，所有涉及硬件通信的代码都必须提供纯软件仿真逻辑（Mock），确保没有连仪器时程序也能正常跑通。

</details>

---

<details>
<summary><b>📌 一、 团队分组与仓库认领（我是哪一组？该去哪里写代码？）</b></summary>

系统由 6 个独立私有仓库组成，请根据组长为你分配的方向，认领你专属的仓库进行开发，不要走错仓库：

| 仓库名称 | 专属仓库地址 (HTTPS) | 职责与负责内容 | 建议加入的组员 |
| :--- | :--- | :--- | :--- |
| **`calib-core`** | `https://github.com/EMF-testing-cug/calib-core.git` | 通用仪器通信底层（SCPI）、PaddleOCR 视觉读数、计量算法、CSV 导出规范 | 底层核心支撑组 |
| **`app-tem`** | `https://github.com/EMF-testing-cug/app-tem.git` | TEM 室界面与业务流程（10kHz~300MHz 频段各向同性、场强幅值校准） | TEM 室业务组 |
| **`app-gtem`** | `https://github.com/EMF-testing-cug/app-gtem.git` | µTEM/GTEM 室界面与流程（10MHz~1GHz 场强计定标与校准测试） | GTEM 室业务组 |
| **`app-chamber`** | `https://github.com/EMF-testing-cug/app-chamber.git` | 微波暗室界面与流程（1GHz~18GHz 射频校准、双功放切换、自动对准） | 暗室射频业务组 |
| **`app-magnetic`** | `https://github.com/EMF-testing-cug/app-magnetic.git` | 亥姆赫兹线圈工频磁场校准前端（工频磁场线性度、底噪扣除、单位换算） | 工频磁场业务组 |
| **`calib-integration`** | `https://github.com/EMF-testing-cug/calib-integration.git` | 整个工程的联调测试脚本、虚拟仪器仿真服务、最终一键打包安装包 | 集成与测试组 |

</details>

---

<details>
<summary><b>⚙️ 二、 首次电脑环境准备（只做一次，跟着敲就行）</b></summary>

### 1. 配置你的 Git 名字与邮箱（让系统认出你是谁）

按 `Win + R` 键，输入 `powershell` 回车打开终端，依次输入并执行以下两行（把引号里的内容换成你自己的）：

```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```

### 2. 开启系统密码自动记忆（告别每次输密码）
在终端中继续输入这一行命令：

```Bash
git config --global credential.helper manager
作用：开启后，以后在拉取或上传代码时，Windows 会自动唤起浏览器一键授权，无需手动反复输入 Token 密钥。
```

### 3. 网络连不上 GitHub 的解决办法
如果终端报错 Recv failure: Connection was reset 或打开网页转圈： 校园网有时候可以直连github，或使用magic
</details>

---

<details>
<summary><b> 三、如何下载和上传代码</b></summary>
### 步骤 1：下载代码（以 app-tem 组为例）
在电脑中新建一个工作文件夹（例如 D:\EMF_Project），在该目录下按住 Shift + 鼠标右键 选择 “在此处打开 PowerShell 窗口”，执行：

```Bash
# 1. 克隆下载仓库
git clone [https://github.com/EMF-testing-cug/app-tem.git](https://github.com/EMF-testing-cug/app-tem.git)

# 2. 进入项目文件夹
cd app-tem

# 3. 切换到统一开发主干 develop 分支（必须在 develop 基础上做开发）
git checkout develop
git pull origin develop
```

### 步骤 2：建立属于你自己的专属分支（独立沙盒，随便折腾）
为了防止大家互相覆盖代码，每个人写代码前必须新建一个专属分支。

分支命名规则：feature/姓名拼音-你要做的功能

```Bash
# 示例：张三负责编写功率计测试
git checkout -b feature/zhangsan-power-meter
敲完后看到 Switched to a new branch 就说明你已经进入专属分支了，在这里改任何文件都不会影响其他人！
```

### 步骤 3：用 PyCharm 打开并安装依赖
打开 PyCharm，选择 Open，选中刚才下载的 app-tem 文件夹。

打开 PyCharm 下方的 Terminal（终端），安装团队依赖与质检工具：

```Bash
pip install -r requirements.txt
pip install ruff pytest
对照各自组长分派的任务开始编写代码。
```

### 步骤 4：本地“安检”自测（必做！没做上传必报红叉）
在准备上传代码前，必须在 PyCharm 底部终端运行以下两行命令，通过电脑的自动检测：

```Bash
# 1. 自动把你的代码排版整齐、修复空行和引用顺序
python -m ruff check --fix .
python -m ruff format .

# 2. 跑一遍测试用例，确保你的代码没有把已有功能改挂
pytest tests/
看结果：如果终端最后显示绿色的一行（例如 x passed），说明你的代码完全合格，可以上传！
```

### 步骤 5：提交并上传到 GitHub
在终端中依次输入以下三条命令：

```Bash
# 1. 收集你修改过的所有文件
git add .

# 2. 写一句简短的说明，告诉大家你改了什么
git commit -m "feat: 完成功率计基础通信与量程切换功能"

# 3. 推送到云端专属开发分支
git push -u origin feature/zhangsan-power-meter
```

### 步骤 6：在网页上发起合并申请（Pull Request）
打开浏览器进入你的仓库页面（如 https://github.com/EMF-testing-cug/app-tem）。

页面顶部会弹出一个黄色横幅，点击右侧绿色的 Compare & pull request。

⚠️ 极其关键的一步（核对目标）：

左边 base（目标分支）：必须下拉改成 develop（严禁直接选 main）。

右边 compare（来源分支）：确认是你刚才推上来的 feature/zhangsan-...。

标题写明改动内容，点击右下角绿色的 Create pull request。

提交后看页面中间的自动化安检状态：

如果出现 ✔️ All checks have passed（绿勾）：大功告成！直接在群里联系组长进行 Review 并合并入库。

如果出现 ❌ All checks have failed（红叉）：别慌，点击下方的 Details 看看报了什么错，在本地改好后，重新输入 git add .、git commit、git push 即可，网页会自动重新检测！

为了让大家在没有真实硬件时也能在电脑上调试界面和逻辑，底层所有驱动都必须实现如下规范模式：

```bash
Python
class PowerMeterDriver:
    """功率计驱动示例：包含真实 SCPI 通信与 Mock 纯软件仿真"""

    def __init__(self, resource_name: str, mock_mode: bool = True):
        self.resource_name = resource_name
        self.mock_mode = mock_mode
        self.is_connected = False

    def connect(self) -> bool:
        if self.mock_mode:
            print(f"[MOCK 仿真模式] 成功模拟连接设备: {self.resource_name}")
            self.is_connected = True
            return True

        # 真实硬件通信（通过 VISA 库连接）
        import pyvisa

        rm = pyvisa.ResourceManager()
        self.session = rm.open_resource(self.resource_name)
        self.is_connected = True
        return True

    def read_power_dbm(self) -> float:
        """读取实测功率 (dBm)"""
        if self.mock_mode:
            # 仿真返回微小的波动值，方便前端界面观察曲线刷新
            import random

            return round(-10.0 + random.uniform(-0.1, 0.1), 3)

        # 真实仪器发送 SCPI 查询指令
        raw_val = self.session.query("READ:POW?")
        return float(raw_val)
```
</details>

---

<details>
<summary><b> QA</b></summary>
### Q1：提示 Authentication failed / 提示输入账号密码失败？
原因：GitHub 已经不支持输入普通登录密码。

解决办法：在终端输入 git config --global credential.helper manager。下次推送时 Windows 会弹出一个网页登录小窗口，在浏览器点击绿色按钮 Authorize 授权一次即可。

###Q2：别人比我先合并了代码，我怎么获取别人的最新代码？
在发起 PR 之前，需要先拉取最新的 develop 主干合并到自己的分支：

```Bash
# 1. 切换到 develop 并拉取全队最新进展
git checkout develop
git pull origin develop

# 2. 切换回你的个人分支，把最新的主干合进来
git checkout feature/你的分支名
git merge develop
如果提示冲突，在 PyCharm 里会弹窗提示你保留哪边的代码，点击接受后重新保存即可。
```

### Q3：代码写乱套了，想彻底放弃这次没保存的修改，恢复原样？
只要你还没输入过 git commit，在终端输入这一行命令，所有改动立刻还原：

```Bash
git restore .
```

### Q4：为什么我的测试代码在 Python 3.8 报 TypeError: 'type' object is not subscriptable？
原因：Python 3.8 对小写的泛型类型注解（如 list[float]、tuple[...]）支持不完善。

解决办法：在那个报错的代码文件最顶端第一行加入以下代码即可完全兼容[cite: 6]：

```bash
Python
from __future__ import annotations
```

</details>
