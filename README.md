# AIGov-Insight-Agent

[![Eversec](https://img.shields.io/badge/Eversec-CN-blue?link=https://www.eversec.com.cn)](https://www.eversec.com.cn) [![Eversec.AI](https://img.shields.io/badge/Eversec-AI-green?link=https://www.eversec.com.cn)](https://www.eversec.com.cn) 
[![GitHub stars](https://img.shields.io/github/stars/Eversec-AI/AIGov-Insight-Agent?style=social)](https://github.com/Eversec-AI/AIGov-Insight-Agent/stargazers) [![GitHub forks](https://img.shields.io/github/forks/Eversec-AI/AIGov-Insight-Agent?style=social)](https://github.com/Eversec-AI/AIGov-Insight-Agent/network/members) [![GitHub release](https://img.shields.io/github/v/release/eversec/AIGov-Insight-Agent?include_prereleases)](https://github.com/eversec/AIGov-Insight-Agent/releases)

基于 eBPF 技术的 AI 安全监控探针，用于观测 AI 智能体 / 🦞 Openclaw 和 LLM 应用的网络流量、ROS2 通信以及系统行为。

> **注意**: 本仓库仅包含 AI 智能数据采集探针， Web 可视化层参见 [AIGov-Insight Web](https://github.com/Eversec-ai/AIGov-Insight-Web)。

## 系统要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Ubuntu 22.04 LTS |
| 架构 | x86_64 |
| 内核版本 | ≥ 5.4 |


## 安装

### 获取安装包

从 [Github Release 发布页](https://github.com/Eversec-ai/AIGov-Insight-Agent/releases) 下载最新版本安装包

### 安装 DEB 包

```bash
sudo dpkg -i ai-sec-agent_*.deb
```

## 快速开始

```bash
# 运行配置向导
sudo ai-sec-ctl setup

# 查看配置
sudo ai-sec-ctl config

# 启动服务
sudo ai-sec-ctl start

# 设置开机自启
sudo ai-sec-ctl enable

# 查看服务状态
ai-sec-ctl status
```

## 核心能力

### 1. AI 流量监控

通过 TC (Traffic Control) eBPF 程序监控网络流量，支持多种 AI 协议解析：

| 协议 | 说明 |
|------|------|
| **Agent** | Dify Agent 交互流量 |
| **Workflow** | Workflow 工作流交互 |
| **MCP** | Model Context Protocol 协议 |
| **vLLM** | vLLM 推理服务 |
| **Ollama** | Ollama 本地模型服务 |
| **OpenClaw** | OpenClaw AI Agent WebSocket |

### 2. Agent 交互监控

- 会话追踪（conversation_id、message_id）
- 用户提问与 AI 回答记录
- 思考过程（agent_thought）监控
- 工具调用详情（工具名称、输入参数）
- Token 统计（prompt_tokens、completion_tokens）
- 性能指标（响应延迟、迭代次数）

### 3. Workflow 工作流监控

- 节点执行记录（最多 32 个节点）
- 支持节点类型：llm、tool、code、if_else、knowledge-retrieval
- 执行状态：succeeded/failed/skipped
- 执行时间统计

### 4. RAG 检索监控

- 检索命中数量、字符命中数量
- 相似度得分
- 知识库名称、文档名称
- 检索片段摘要

### 5. MCP 协议监控

- 方法类型：initialize、tools/call
- 客户端/服务端信息
- 工具调用详情

### 6. LLM 服务监控

- 支持流式/非流式请求
- 对话历史轮数统计
- 模型版本信息

### 7. ROS2 监控

通过 uprobe 监控 ROS2 机器人节点通信：

- 监控 `rcl_node_init`、`rmw_publish`、`rmw_take` 等关键函数
- 捕获话题（Topic）、消息内容、节点名称、命名空间

### 8. 文件访问监控

监控敏感文件访问行为：

- 支持自定义敏感文件列表（如 `/etc/passwd`, `*.pem`, `*.env`）
- 支持文件包含模式（如 `*.conf`, `/etc/*`）
- 支持文件排除模式（如 `/tmp/*`）
- 支持风险等级过滤（HIGH/MEDIUM/LOW/INFO）

### 9. 进程执行监控

监控命令执行行为：

- 捕获进程执行事件
- 记录命令行参数、父进程信息
- 支持风险等级过滤

## 服务管理

```bash
# 服务控制
sudo ai-sec-ctl start      # 启动服务
sudo ai-sec-ctl stop       # 停止服务
sudo ai-sec-ctl restart    # 重启服务
sudo ai-sec-ctl enable     # 开机自启
sudo ai-sec-ctl disable    # 禁用开机自启

# 状态查看
ai-sec-ctl status          # 查看服务状态
ai-sec-ctl config          # 查看当前配置
ai-sec-ctl logs            # 查看日志

```

## 配置说明

### 配置文件位置

| 文件 | 路径 |
|------|------|
| 主配置文件 | `/etc/ai-sec-agent/agent.conf` |
| 心跳配置文件 | `/etc/ai-sec-agent/heartbeat.conf` |
| 话单日志目录 | `/var/log/ai-sec-agent/data` |

### 配置向导

运行 `sudo ai-sec-ctl setup` 进入交互式配置向导，支持配置：

1. **网络接口**：选择监控的网络接口（如 eth0, ens33）
2. **观测端口**：配置监控的服务端口（如 80, 443, 8001, 11434）
3. **部署模式**：auto（智能识别）/ server（服务端）/ client（客户端）
4. **流量方向**：全双工/仅入向/仅出向
5. **流超时设置**：空闲超时（默认 10 秒）、最大超时（默认 30 秒）
6. **文件监听**：启用/禁用、敏感文件列表、风险等级过滤
7. **进程执行监控**：启用/禁用、风险等级过滤
8. **代理标识**：Agent ID（默认使用 /etc/machine-id）
9. **主机 IP**：探针 IP 地址
10. **日志输出**：日志目录、单文件大小限制、刷新间隔

## 卸载

```bash
sudo dpkg -r ai-sec-agent
```

## 技术特性

- **高性能低侵入**：基于 eBPF 内核态技术，无需修改应用程序，低性能开销
- **模块化解析器**：可扩展的协议解析框架，支持优先级匹配
- **流量汇聚**：自动关联请求和响应，支持流式响应（SSE）拼接
- **智能识别**：自动识别流量方向和服务类型

## 恒安嘉新

网站：http://www.eversec.com.cn  
