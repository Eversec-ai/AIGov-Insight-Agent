<div align="center">

# AI-SEC-Agent

**恒安嘉新大模型与智能体安全治理平台 - AI 智能体全链路可观测探针**

基于 eBPF 技术的 AI 智能体全链路可观测探针，用于观测 AI 智能体和 LLM 应用的网络流量、ROS2 通信以及系统指令执行、系统文件访问等行为。AIGov-Insight Web 与 AIGov-Insight Agent 共同构成大模型与智能体安全治理平台的核心双引擎。


[![Eversec](https://img.shields.io/badge/Eversec-CN-blue?link=https://www.eversec.com.cn)](https://www.eversec.com.cn) [![Eversec.AI](https://img.shields.io/badge/Eversec-AI-green?link=https://www.eversec.com.cn)](https://www.eversec.com.cn) 
[![GitHub stars](https://img.shields.io/github/stars/Eversec-AI/AIGov-Insight-Agent?style=social)](https://github.com/Eversec-AI/AIGov-Insight-Agent/stargazers) [![GitHub forks](https://img.shields.io/github/forks/Eversec-AI/AIGov-Insight-Agent?style=social)](https://github.com/Eversec-AI/AIGov-Insight-Agent/network/members) [![GitHub release](https://img.shields.io/github/v/release/Eversec-AI/AIGov-Insight-Agent?include_prereleases)](https://github.com/Eversec-AI/AIGov-Insight-agent/releases)
[![Platform](https://img.shields.io/badge/Platform-Ubuntu%2022.04%20LTS-orange)](https://ubuntu.com/) [![Architecture](https://img.shields.io/badge/Arch-x86__64-blue)](https://en.wikipedia.org/wiki/X86-64) [![Kernel](https://img.shields.io/badge/Kernel-%3E%3D5.4-green)](https://www.kernel.org/) [![eBPF](https://img.shields.io/badge/Technology-eBPF-purple)](https://ebpf.io/)

[English](#english) | [中文文档](#中文文档)

[功能特性](#-功能特性) • [安装](#-安装) • [快速开始](#-快速开始) • [配置向导](#-配置向导) • [服务管理](#-服务管理)

</div>

# 中文文档

## 📋 系统要求

| 项目 | 规格 |
|------|------|
| **操作系统** | Ubuntu 22.04 LTS |
| **架构** | x86_64 |
| **内核** | ≥ 5.4 |


---

## 🚀 功能特性

### AI 流量监控

通过 TC (Traffic Control) eBPF 程序监控 AI 智能体和 LLM 应用的 HTTP/HTTPS 流量。

| 协议 | 说明 |
|------|------|
| **Agent** | Dify Agent 交互流量 |
| **Workflow** | Workflow 工作流交互 |
| **MCP** | Model Context Protocol 协议 |
| **vLLM** | vLLM 推理服务 |
| **Ollama** | Ollama 本地模型服务 |
| **OpenClaw** | OpenClaw AI Agent WebSocket |

### Agent 交互监控

- 📝 会话追踪（conversation_id、message_id）
- 💬 用户提问与 AI 回答记录
- 🧠 思考过程（agent_thought）监控
- 🔧 工具调用详情（工具名称、输入参数）
- 📊 Token 统计（prompt_tokens、completion_tokens）
- ⏱️ 性能指标（响应延迟、迭代次数）

### Workflow 工作流监控

- 节点执行记录（最多 32 个节点）
- 支持节点类型：`llm`、`tool`、`code`、`if_else`、`knowledge-retrieval`
- 执行状态：`succeeded` / `failed` / `skipped`
- 执行时间统计

### RAG 检索监控

- 检索命中数量、字符命中数量
- 相似度得分
- 知识库名称、文档名称
- 检索片段摘要

### MCP 协议监控

- 方法类型：`initialize`、`tools/call`
- 客户端/服务端信息
- 工具调用详情

### LLM 服务监控

- 支持流式/非流式请求
- 对话历史轮数统计
- 模型版本信息

### ROS2 监控

通过 uprobe 监控 ROS2 机器人节点通信：

- 关键函数：`rcl_node_init`、`rmw_publish`、`rmw_take`
- 捕获数据：话题（Topic）、消息内容、节点名称、命名空间

### 文件访问监控

监控敏感文件访问行为：

- 自定义敏感文件列表（如 `/etc/passwd`、`*.pem`、`*.env`）
- 文件包含模式（如 `*.conf`、`/etc/*`）
- 文件排除模式（如 `/tmp/*`）
- 风险等级过滤（`HIGH` / `MEDIUM` / `LOW` / `INFO`）

### 进程执行监控

监控命令执行行为：

- 进程执行事件
- 命令行参数、父进程信息
- 风险等级过滤

---

## 📦 安装

### 步骤 1：获取安装包

从 [Github Release 发布页](https://github.com/Eversec-ai/AIGov-Insight-Agent/releases) 下载最新版本安装包

### 步骤 2：安装 DEB 包

```bash
sudo dpkg -i ai-sec-agent_*.deb
```

## ⚡ 快速开始

```bash
# 运行配置向导
sudo ai-sec-ctl setup

# 启动服务
sudo ai-sec-ctl start

# 设置开机自启
sudo ai-sec-ctl enable

# 查看服务状态
ai-sec-ctl status
```

---

## ⚙️ 配置向导

运行 `sudo ai-sec-ctl setup` 启动交互式配置向导。本节详细解释每个配置选项的含义。

### 1. 监控模式

根据使用场景选择监控模式：

| 选项 | 模式 | 说明 |
|------|------|------|
| `1` | **tc** | 网络流量监控（HTTP/HTTPS）- **推荐用于 AI/LLM** |
| `2` | **ros** | ROS2 消息监控 - 适用于机器人应用 |

### 2. TC 模式配置

#### 2.1 网络接口

选择要监控的网络接口：

```
[1] eth0
[2] ens33
[3] enp0s3
```

- **单个接口**：输入接口名称或编号（如 `eth0` 或 `1`）
- **多个接口**：逗号分隔（如 `eth0,ens33`）
- **所有接口**：输入 `any`（⚠️ 不推荐，可能产生重复流量）

> 💡 **提示**：选择 AI 服务对外暴露的接口。使用 `ip addr` 查看可用接口。

#### 2.2 观测端口

指定 AI/LLM 服务运行的端口：

```
示例: 80,3000,7000,8000,11434
默认: 80
```

常见端口示例：
- Dify: `80`, `3000`
- vLLM: `8000`
- Ollama: `11434`
- OpenClaw: `7000`

> 💡 **提示**：多个服务使用逗号分隔。

#### 2.3 服务端端口规则（可选）

预设服务端端口，用于智能纠正请求/响应方向：

```
示例: 80,7000,8000
默认: （空 - 不启用）
```

**作用**：当探针检测到这些端口的流量时，会自动识别为服务端方向，确保日志中请求/响应映射正确。

> 💡 **提示**：输入 AI 服务作为服务端（接收请求）的端口。

#### 2.4 部署模式

根据服务器角色选择部署模式：

| 选项 | 模式 | 说明 |
|------|------|------|
| `1` | **auto** | 智能识别（根据目标端口 80/443/8001）- **推荐** |
| `2` | **server** | 本机是服务端（如 Dify 服务器） |
| `3` | **client** | 本机是客户端（如 PC） |

**工作原理**：
- `auto`：根据标准服务端口自动判断方向
- `server`：将入向流量视为请求，出向流量视为响应
- `client`：将出向流量视为请求，入向流量视为响应

#### 2.5 流量方向

选择监控的流量方向：

| 选项 | 方向 | 说明 |
|------|------|------|
| `1` | **全双工** | 同时监控入向和出向 - **默认** |
| `2` | **仅入向** | 仅监控入站流量 |
| `3` | **仅出向** | 仅监控出站流量 |

> 💡 **提示**：使用全双工获得完整可见性。使用单向可降低开销。

#### 2.6 流超时设置

配置流会话超时参数：

| 参数 | 默认值 | 说明 |
|------|--------|------|
| **空闲超时** | 10s | 空闲此时间后关闭流 |
| **最大超时** | 30s | 无论是否活跃，流的最大生命周期 |

**工作原理**：
- 当在 `空闲超时` 秒内没有看到数据包时，流被认为已完成
- 即使仍在活跃，流也会在 `最大超时` 秒后强制关闭

> 💡 **提示**：长时间对话可增加超时值；高流量环境可减少。

#### 2.7 文件监听配置

启用并配置文件访问监控：

| 选项 | 说明 |
|------|------|
| `1` | 启用文件监听 |
| `2` | 禁用文件监听（默认） |

**启用后可用选项**：

| 参数 | 示例 | 说明 |
|------|------|------|
| **文件包含模式** | `*.conf,/etc/*` | 仅监控匹配这些模式的文件 |
| **文件排除模式** | `/tmp/*` | 排除匹配这些模式的文件 |
| **监控所有文件** | `y/N` | 监控所有文件访问（不仅限敏感文件） |
| **自定义敏感文件** | `/etc/passwd,*.pem` | 额外的敏感文件模式 |
| **风险等级过滤** | `HIGH,MEDIUM,LOW` | 按风险等级过滤 |
| **调试输出** | `y/N` | 启用详细文件监听日志 |

**风险等级**：
- `HIGH`：关键文件（如 `/etc/shadow`、`*.key`）
- `MEDIUM`：重要文件（如 `/etc/passwd`、`*.pem`）
- `LOW`：一般敏感文件（如 `*.env`、`*.conf`）
- `INFO`：普通文件访问

#### 2.8 进程执行监控

启用并配置进程执行监控：

| 选项 | 说明 |
|------|------|
| `1` | 启用进程执行监控 |
| `2` | 禁用进程执行监控（默认） |

**启用后可用选项**：

| 参数 | 默认值 | 说明 |
|------|--------|------|
| **风险等级过滤** | `HIGH,MEDIUM,LOW,INFO` | 按风险等级过滤 |
| **调试输出** | `y/N` | 启用详细执行日志 |

**风险等级**：
- `HIGH`：危险命令（如 `rm -rf`、`chmod 777`）
- `MEDIUM`：权限相关命令（如 `sudo`、`su`）
- `LOW`：系统命令（如 `cat`、`ls`）
- `INFO`：普通命令

#### 2.9 Agent ID

探针实例的唯一标识符：

```
默认: /etc/machine-id（32 字符设备 ID）
```

> 💡 **提示**：使用默认值可自动获得唯一标识。如需自定义标识方案可覆盖。

#### 2.10 主机 IP 地址

探针主机的 IP 地址，用于日志关联：

```
检测到的接口:
[1] 192.168.1.100 (eth0)
[2] 10.0.0.50 (ens33)
```

> 💡 **提示**：选择可从日志聚合服务器访问的 IP 地址。

### 3. 日志输出配置

配置日志输出设置：

| 参数 | 默认值 | 说明 |
|------|--------|------|
| **日志目录** | `/var/log/ai-sec-agent/data` | 日志文件目录 |
| **单文件最大** | 100 MB | 单个日志文件最大大小 |
| **单文件最大条目** | 1000 | 单文件最大日志条目数 |
| **刷新间隔** | 10s | 强制刷新缓冲区到磁盘 |
| **直写磁盘** | `N` | 使用 O_DIRECT 写入磁盘 |

> 💡 **提示**：关键监控场景启用直写磁盘，确保立即持久化。

### 4. 调试选项（仅 TC 模式）

高级调试选项用于故障排查：

| 参数 | 选项 | 默认值 | 说明 |
|------|------|--------|------|
| **eBPF 内核日志级别** | `off`, `basic`, `verbose`, `full` | `basic` | 内核态 eBPF 调试 |
| **libbpf 日志级别** | `warn`, `info`, `debug` | `warn` | libbpf 库日志 |
| **数据包打印** | `y/N` | `N` | 打印原始数据包 |
| **流打印** | `y/N` | `N` | 打印流信息 |
| **解析器打印** | `y/N` | `N` | 打印解析器输出 |
| **禁用流汇聚** | `y/N` | `N` | 单包模式处理 |

> ⚠️ **警告**：调试选项可能显著影响性能，仅用于故障排查。

---

## 🎮 服务管理

### 基本命令

```bash
# 服务控制
sudo ai-sec-ctl start      # 启动服务
sudo ai-sec-ctl stop       # 停止服务
sudo ai-sec-ctl restart    # 重启服务

# 开机自启
sudo ai-sec-ctl enable     # 启用开机自启
sudo ai-sec-ctl disable    # 禁用开机自启

# 状态查看
ai-sec-ctl status          # 查看服务状态
ai-sec-ctl config          # 查看当前配置
```

### 心跳服务

```bash
# 配置心跳
sudo ai-sec-ctl setup-hb

# 心跳服务控制
sudo ai-sec-ctl start-hb
sudo ai-sec-ctl stop-hb
sudo ai-sec-ctl restart-hb

# 心跳状态
ai-sec-ctl status-hb
ai-sec-ctl config-hb
```

### 诊断命令

```bash
# 检查系统环境
sudo ai-sec-ctl check

# 查看日志
ai-sec-ctl logs

# 清理 TC 钩子（服务异常退出时使用）
sudo ai-sec-ctl clean
```

---

## 📁 文件位置

| 组件 | 路径 |
|------|------|
| 主程序 | `/usr/bin/ai-sec-agent` |
| 控制工具 | `/usr/bin/ai-sec-ctl` |
| 心跳客户端 | `/usr/bin/ai-sec-heartbeat` |
| 主配置文件 | `/etc/ai-sec-agent/agent.conf` |
| 心跳配置文件 | `/etc/ai-sec-agent/heartbeat.conf` |
| 日志目录 | `/var/log/ai-sec-agent/data` |
| Systemd 服务 | `/lib/systemd/system/ai-sec-agent.service` |

---

## 🗑️ 卸载

```bash
sudo dpkg -r ai-sec-agent
```

---

## 🔧 技术特性

| 特性 | 说明 |
|------|------|
| **基于 eBPF** | 内核态监控，无需修改应用程序 |
| **低开销** | 最小性能影响，适合生产环境 |
| **模块化解析器** | 可扩展的协议解析框架 |
| **流量汇聚** | 自动关联请求/响应，支持 SSE |
| **智能识别** | 自动识别流量方向和服务类型 |

---

## 🏢 EverSec

🌐 网站: http://www.eversec.com.cn  

---

<div align="center">

**Made with ❤️ by EverSec**

</div>


# English

<div align="center">

# AI-SEC-Agent

**Enterprise-Grade AI Security Monitoring Probe**

An eBPF-based AI security monitoring probe for observing network traffic, ROS2 communication, and system behavior of AI agents and LLM applications.

[![Platform](https://img.shields.io/badge/Platform-Ubuntu%2022.04%20LTS-orange)](https://ubuntu.com/) [![Architecture](https://img.shields.io/badge/Arch-x86__64-blue)](https://en.wikipedia.org/wiki/X86-64) [![Kernel](https://img.shields.io/badge/Kernel-%3E%3D5.4-green)](https://www.kernel.org/) [![eBPF](https://img.shields.io/badge/Technology-eBPF-purple)](https://ebpf.io/)

[Features](#-features-1) • [Installation](#-installation-1) • [Quick-Start](#-quick-start-1) • [Configuration](#-configuration-wizard-1) • [Service-Management](#-service-management-1)

</div>

---

## 📋 Requirements

| Requirement | Specification |
|-------------|---------------|
| **OS** | Ubuntu 22.04 LTS |
| **Architecture** | x86_64 |
| **Kernel** | ≥ 5.4 with eBPF |

---

## 🚀 Features

### AI Traffic Monitoring

Monitor HTTP/HTTPS traffic of AI Agents and LLM applications via TC (Traffic Control) eBPF programs.

| Protocol | Description |
|----------|-------------|
| **Agent** | Dify Agent interaction traffic |
| **Workflow** | Workflow orchestration traffic |
| **MCP** | Model Context Protocol |
| **vLLM** | vLLM inference service |
| **Ollama** | Ollama local model service |
| **OpenClaw** | OpenClaw AI Agent WebSocket |

### Agent Interaction Monitoring

- 📝 Session tracking (conversation_id, message_id)
- 💬 User prompts and AI responses
- 🧠 Thought process monitoring (agent_thought)
- 🔧 Tool invocation details (tool name, input parameters)
- 📊 Token statistics (prompt_tokens, completion_tokens)
- ⏱️ Performance metrics (latency, iterations)

### Workflow Monitoring

- Node execution records (up to 32 nodes)
- Supported node types: `llm`, `tool`, `code`, `if_else`, `knowledge-retrieval`
- Execution status: `succeeded` / `failed` / `skipped`
- Execution time statistics

### RAG Retrieval Monitoring

- Retrieval hit count, character hit count
- Similarity scores
- Knowledge base name, document name
- Retrieval segment summary

### MCP Protocol Monitoring

- Method types: `initialize`, `tools/call`
- Client/server information
- Tool invocation details

### LLM Service Monitoring

- Streaming / non-streaming requests
- Conversation history rounds
- Model version information

### ROS2 Monitoring

Monitor ROS2 robot node communication via uprobe:

- Key functions: `rcl_node_init`, `rmw_publish`, `rmw_take`
- Captured data: Topics, messages, node names, namespaces

### File Access Monitoring

Monitor sensitive file access behavior:

- Custom sensitive file list (e.g., `/etc/passwd`, `*.pem`, `*.env`)
- File include patterns (e.g., `*.conf`, `/etc/*`)
- File exclude patterns (e.g., `/tmp/*`)
- Risk level filtering (`HIGH` / `MEDIUM` / `LOW` / `INFO`)

### Process Execution Monitoring

Monitor command execution behavior:

- Process execution events
- Command-line arguments, parent process info
- Risk level filtering

---

## 📦 Installation

### Step 1: Download DEB Package

Get DEB Package from [Github Release Page](https://github.com/Eversec-ai/AIGov-Insight-Agent/releases) 

### Step 2: Install DEB Package

```bash
sudo dpkg -i ai-sec-agent_*.deb
```

### Step 3: Verify Installation

```bash
ai-sec-ctl check
```

---

## ⚡ Quick Start

```bash
# Run configuration wizard
sudo ai-sec-ctl setup

# Start the service
sudo ai-sec-ctl start

# Enable auto-start on boot
sudo ai-sec-ctl enable

# Check service status
ai-sec-ctl status
```

---

## ⚙️ Configuration Wizard

Run `sudo ai-sec-ctl setup` to launch the interactive configuration wizard. This section explains each configuration option in detail.

### 1. Monitoring Mode

Select the monitoring mode based on your use case:

| Option | Mode | Description |
|--------|------|-------------|
| `1` | **tc** | Network traffic monitoring (HTTP/HTTPS) - **Recommended for AI/LLM** |
| `2` | **ros** | ROS2 message monitoring - For robotics applications |

### 2. Mode Configuration

#### 2.1 Network Interface

Select the network interface(s) to monitor:

```
[1] eth0
[2] ens33
[3] enp0s3
```

- **Single interface**: Enter the interface name or number (e.g., `eth0` or `1`)
- **Multiple interfaces**: Comma-separated (e.g., `eth0,ens33`)
- **All interfaces**: Enter `any` (⚠️ Not recommended - may cause duplicate traffic)

> 💡 **Tip**: Select the interface where your AI services are exposed. Use `ip addr` to check available interfaces.

#### 2.2 Observation Ports

Specify the ports where your AI/LLM services are running:

```
Example: 80,3000,7000,8000,11434
Default: 80
```

Common port examples:
- Dify: `80`, `3000`
- vLLM: `8000`
- Ollama: `11434`
- OpenClaw: `7000`

> 💡 **Tip**: Use comma-separated values for multiple services.

#### 2.3 Server Port Rules (Optional)

Predefined server ports for intelligent request/response direction correction:

```
Example: 80,7000,8000
Default: (empty - disabled)
```

**Purpose**: When the probe detects traffic on these ports, it automatically identifies the direction as server-side, ensuring correct request/response mapping in logs.

> 💡 **Tip**: Enter ports where your AI services act as servers (receiving requests).

#### 2.4 Deployment Mode

Select the deployment mode based on your server role:

| Option | Mode | Description |
|--------|------|-------------|
| `1` | **auto** | Intelligent detection based on target ports (80/443/8001) - **Recommended** |
| `2` | **server** | This machine is a server (e.g., Dify server) |
| `3` | **client** | This machine is a client (e.g., PC) |

**How it works**:
- `auto`: Automatically determines direction based on standard service ports
- `server`: Treats incoming traffic as requests, outgoing as responses
- `client`: Treats outgoing traffic as requests, incoming as responses

#### 2.5 Traffic Direction

Select which traffic direction to monitor:

| Option | Direction | Description |
|--------|-----------|-------------|
| `1` | **Full-duplex** | Monitor both ingress and egress - **Default** |
| `2` | **Ingress only** | Monitor incoming traffic only |
| `3` | **Egress only** | Monitor outgoing traffic only |

> 💡 **Tip**: Use full-duplex for complete visibility. Use single direction to reduce overhead.

#### 2.6 Flow Timeout Settings

Configure flow session timeout parameters:

| Parameter | Default | Description |
|-----------|---------|-------------|
| **Idle timeout** | 10s | Close flow after this idle period |
| **Max timeout** | 30s | Maximum flow lifetime regardless of activity |

**How it works**:
- A flow is considered complete when no packets are seen for `idle timeout` seconds
- A flow is forcibly closed after `max timeout` seconds even if still active

> 💡 **Tip**: Increase for long-running conversations; decrease for high-traffic environments.

#### 2.7 File Monitoring Configuration

Enable and configure file access monitoring:

| Option | Description |
|--------|-------------|
| `1` | Enable file monitoring |
| `2` | Disable file monitoring (Default) |

**When enabled, additional options are available**:

| Parameter | Example | Description |
|-----------|---------|-------------|
| **File include pattern** | `*.conf,/etc/*` | Only monitor files matching these patterns |
| **File exclude pattern** | `/tmp/*` | Exclude files matching these patterns |
| **Monitor all files** | `y/N` | Monitor all file access (not just sensitive files) |
| **Custom sensitive files** | `/etc/passwd,*.pem` | Additional sensitive file patterns |
| **Risk level filter** | `HIGH,MEDIUM,LOW` | Filter by risk level |
| **Debug output** | `y/N` | Enable verbose file monitoring logs |

**Risk Levels**:
- `HIGH`: Critical files (e.g., `/etc/shadow`, `*.key`)
- `MEDIUM`: Important files (e.g., `/etc/passwd`, `*.pem`)
- `LOW`: General sensitive files (e.g., `*.env`, `*.conf`)
- `INFO`: Normal file access

#### 2.8 Process Execution Monitoring

Enable and configure process execution monitoring:

| Option | Description |
|--------|-------------|
| `1` | Enable process execution monitoring |
| `2` | Disable process execution monitoring (Default) |

**When enabled, additional options are available**:

| Parameter | Default | Description |
|-----------|---------|-------------|
| **Risk level filter** | `HIGH,MEDIUM,LOW,INFO` | Filter by risk level |
| **Debug output** | `y/N` | Enable verbose execution logs |

**Risk Levels**:
- `HIGH`: Dangerous commands (e.g., `rm -rf`, `chmod 777`)
- `MEDIUM`: Privilege-related commands (e.g., `sudo`, `su`)
- `LOW`: System commands (e.g., `cat`, `ls`)
- `INFO`: Normal commands

#### 2.9 Agent ID

Unique identifier for this probe instance:

```
Default: /etc/machine-id (32-character device ID)
```

> 💡 **Tip**: Use the default for automatic unique identification. Override for custom identification schemes.

#### 2.10 Host IP Address

IP address of the probe host for log correlation:

```
Detected interfaces:
[1] 192.168.1.100 (eth0)
[2] 10.0.0.50 (ens33)
```

> 💡 **Tip**: Select the IP address that is reachable from your log aggregation server.

### 3. Log Output Configuration

Configure log output settings:

| Parameter | Default | Description |
|-----------|---------|-------------|
| **Log directory** | `/var/log/ai-sec-agent/data` | Directory for log files |
| **Max file size** | 100 MB | Maximum size per log file |
| **Max entries per file** | 1000 | Maximum log entries per file |
| **Flush interval** | 10s | Force flush buffer to disk |
| **Direct I/O** | `N` | Use O_DIRECT for disk writes |

> 💡 **Tip**: Enable Direct I/O for critical monitoring to ensure immediate disk persistence.

### 4. Debug Options (TC Mode Only)

Advanced debugging options for troubleshooting:

| Parameter | Options | Default | Description |
|-----------|---------|---------|-------------|
| **eBPF kernel log level** | `off`, `basic`, `verbose`, `full` | `basic` | Kernel-space eBPF debugging |
| **libbpf log level** | `warn`, `info`, `debug` | `warn` | libbpf library logging |
| **Packet print** | `y/N` | `N` | Print raw packet data |
| **Flow print** | `y/N` | `N` | Print flow information |
| **Parser print** | `y/N` | `N` | Print parser output |
| **Disable flow aggregation** | `y/N` | `N` | Process each packet individually |

> ⚠️ **Warning**: Debug options may significantly impact performance. Use only for troubleshooting.

---

## 🎮 Service Management

### Basic Commands

```bash
# Service control
sudo ai-sec-ctl start      # Start service
sudo ai-sec-ctl stop       # Stop service
sudo ai-sec-ctl restart    # Restart service

# Auto-start
sudo ai-sec-ctl enable     # Enable auto-start on boot
sudo ai-sec-ctl disable    # Disable auto-start

# Status
ai-sec-ctl status          # View service status
ai-sec-ctl config          # View current configuration
```

### Heartbeat Service

```bash
# Configure heartbeat
sudo ai-sec-ctl setup-hb

# Heartbeat service control
sudo ai-sec-ctl start-hb
sudo ai-sec-ctl stop-hb
sudo ai-sec-ctl restart-hb

# Heartbeat status
ai-sec-ctl status-hb
ai-sec-ctl config-hb
```

### Diagnostics

```bash
# Check system environment
sudo ai-sec-ctl check

# View logs
ai-sec-ctl logs

# Clean up TC hooks (if service crashed)
sudo ai-sec-ctl clean
```

---

## 📁 File Locations

| Component | Path |
|-----------|------|
| Main binary | `/usr/bin/ai-sec-agent` |
| Control tool | `/usr/bin/ai-sec-ctl` |
| Heartbeat client | `/usr/bin/ai-sec-heartbeat` |
| Main config | `/etc/ai-sec-agent/agent.conf` |
| Heartbeat config | `/etc/ai-sec-agent/heartbeat.conf` |
| Log directory | `/var/log/ai-sec-agent/data` |
| Systemd service | `/lib/systemd/system/ai-sec-agent.service` |

---

## 🗑️ Uninstallation

```bash
sudo dpkg -r ai-sec-agent
```

---

## 🔧 Technical Highlights

| Feature | Description |
|---------|-------------|
| **eBPF-based** | Kernel-space monitoring with zero application modification |
| **Low overhead** | Minimal performance impact, suitable for production |
| **Modular parsers** | Extensible protocol parsing framework |
| **Flow aggregation** | Automatic request/response correlation with SSE support |
| **Intelligent detection** | Auto-identify traffic direction and service types |

---

## 🏢 EverSec

🌐 Website: http://www.eversec.com.cn  

---

<div align="center">

**Made with ❤️ by EverSec**

</div>
