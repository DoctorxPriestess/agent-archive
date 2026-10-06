# DSH Agent Presets Archive (`agent-archive`)

DeepSeek Harness (DSH) 工业级 Agent 预设归档库。

> **适用版本**：**DSH 0.2.0**（基于 Cordis 插件容器体系与 `@deepseek-ai/dsh-agent-preset` Bundle 规范）。

本仓库收录并归档了除专用演示文稿架构师（`ppt-architect`）之外的 **三大核心工业级 Agent 预设**：
1. **`coder`（编程助手）**：稳健优先的全能编程架构师
2. **`code-review`（代码安全审查）**：零破坏性只读代码安全审查专家
3. **`sysadmin`（Windows 系统配置与运维）**：状态机驱动的系统级运维专家

这些预设在实际工程实践中经过高度打磨，内置了严苛的安全围栏（Safety Guardrails）、二维证据检验模型、上下文容量生命周期管理与结构化交付物标准。

---

## ⚠️ 重要说明：本地环境差异与配置适配

本仓库归档的预设配置文件（[`cordis.patch.yml`](./cordis.patch.yml)）经过通用脱敏处理，去除了特定宿主机的绝对工作区绑定，但仍包含部分通用的环境假定：
- **工作区路径**：预设中统一绑定至动态工作区 `{{cwd}}`；
- **系统备份与日志目录**：使用 `$env:USERPROFILE\.dsh\.dsh-changes\<timestamp>\` 标准环境变量；
- **系统引导救援示例**：包含标准 WinRE 盘符假设示例（如 `C:\Windows`、`mountvol S: /s`）；
- **平台依赖**：默认假定为 Windows 环境（禁用了 `tool-bash`，启用了 `tool-pwsh`）。

**建议根据您的本地磁盘分区、首选工作区路径或操作系统类型进行针对性配置适配。**

---

## 🤖 推荐安装方式：让 AI Agent 协助本地化适配

**强烈建议直接让您的 AI Agent（当前环境中的编程助手或系统运维 Agent）协助完成本仓库的安装与环境适配，而不是手动死板地修改配置。**

### 为什么推荐让 AI Agent 适配？
1. **自动校准工作区路径**：AI Agent 能自动检测您当前机器的用户名主目录、系统盘符以及您习惯的工作区路径，并将预设规约与您的实际工作区完全对齐；
2. **适配不同软件版本与依赖**：AI Agent 能嗅探当前机器的实际运行环境（如 Python 版本、Node.js 路径、PowerShell 7 vs Windows PowerShell、跨平台 Bash 切换等），微调预设中相应的指令和工具开关；
3. **自动接入 DSH 0.2.0 Profile**：AI Agent 能自动检查 `$env:USERPROFILE\.dsh\profiles\` 下的活动 profile（如 `desktop`），安全地将本地 bundle 链接写入 `package.json`，无缝挂载。

### 📋 复制给 AI Agent 的适配指令模板

您可以直接将以下提示词发送给您的 AI 编程/运维助手：

```text
请帮我把本仓库 (DoctorxPriestess/agent-archive) 中的 Agent 预设安装并适配到我当前的 DSH 0.2.0 环境中：
1. 检查我当前操作系统的环境事实（主目录路径、默认工作区、Shell 类型及工具链版本）；
2. 检查并适配 cordis.patch.yml 中的路径配置，将其对齐为我本机的实际工作区与环境；
3. 将适配后的 Bundle 部署到我本机 DSH 的 plugin-bundles 目录中，并在当前 profile 的 package.json 中挂载；
4. 检查是否有因平台差异（如 Linux/macOS vs Windows）需要调整的插件开关（tool-bash / tool-pwsh）。
```

---

## 🌐 Git 网络与代理配置提示 (7890 端口)

如果从 GitHub 克隆本仓库或后续拉取/推送时遇到网络连接超时或无法访问，可将 Git 网络流量指向本地代理端口 **`7890`**（主流本地代理软件的默认本地监听端口）：

### 1. 单次命令临时走代理克隆
```powershell
git clone -c http.proxy=http://127.0.0.1:7890 -c https.proxy=http://127.0.0.1:7890 https://github.com/DoctorxPriestess/agent-archive.git
```

### 2. 为 Git 配置全局代理
```powershell
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```

*（若日后需要取消全局代理，可执行：`git config --global --unset http.proxy` 与 `git config --global --unset https.proxy`）*

### 3. PowerShell 终端临时会话代理
```powershell
$env:HTTP_PROXY = "http://127.0.0.1:7890"
$env:HTTPS_PROXY = "http://127.0.0.1:7890"
```

---

## 包含的 Agent 概述

| Agent ID | 预设名称 | 角色定位 | 核心安全围栏与要求 |
|---|---|---|---|
| **`coder`** | 编程助手 | 稳健为先的架构师与全能编程 Agent | • 前置需求澄清（Grilling 协议，最多 3 轮，带选项推荐）<br>• 不可逆操作真实授权门禁（删除文件、安装依赖、force-push 必须弹窗授权）<br>• 极简闭环 Bug 修复（严禁顺带重构、严禁防御性吞异常）<br>• 待机 CPU 零占用与路径跨平台安全 |
| **`code-review`** | 代码安全审查 | 只读、零破坏性的代码安全审查专家 | • 严格只读纪律（禁止修改代码、禁止装包、禁止落地 payload）<br>• 提示词注入防御（所有被审文本统一视为非可信数据）<br>• 深度威胁探针（定向扫描凭据、C2 信标、持久化项、反分析、供应链生命周期脚本）<br>• 强制输出八大模块中文专业安全审查报告 |
| **`sysadmin`** | 系统配置 Agent | Windows 本机系统运维与配置专家 | • 不可跳过的 10 步状态机（READ → CLASSIFY → PLAN → APPROVAL → BACKUP → VERIFY-BACKUP → EXECUTE → VERIFY → PERSISTENCE-CHECK → DONE）<br>• 只读诊断在先原则（L0）与 19 项操作红线<br>• 物理磁盘原值备份与系统还原点独立校验（SequenceNumber/Description 校验）<br>• 启动项、UEFI/EFI 与 BCD 专项隔离防护（ESP 临时挂载立即卸载、BCD 备份大小校验） |

---

## 仓库结构

```text
agent-archive/
├── cordis.patch.yml       # DSH 0.2.0 标准 Bundle 补丁文件（定义 coder, code-review, sysadmin）
├── package.json           # DSH Bundle 包描述清单
├── skills/                # 适用于 Agentic Harness 的 Skill 格式实现
│   ├── coder/SKILL.md
│   ├── code-review/SKILL.md
│   └── sysadmin/SKILL.md
└── README.md
```

---

## 通用底层设计哲学

本仓库中的所有 Agent 均遵循以下底层认知与安全准则：
- **真实授权原则 (Real Consent)**：外部工具的自动审批（auto-approvals）不被承认为用户授权，凡涉及环境变更必须经由 `ask_user_question` 获得自然人确认。
- **二维证据体系**：主张严格打上 `FACT` / `INFERENCE` / `UNKNOWN` / `VERIFIED` / `STALE` 标签，区分直接证据与弱信号。
- **停机准则 (Stop Rule)**：证据不足或根因不明时强制标记 `UNKNOWN` 并停止一切文件与系统改动。
- **300k 容量警戒线**：会话接近约 300k tokens 时主动预警，并支持标准化的 Handover 项目交接。
