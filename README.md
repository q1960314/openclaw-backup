# OpenClaw 配置备份

## 备份信息

| 项目 | 值 |
|------|-----|
| **备份时间** | 2026-03-20 13:26 GMT+8 |
| **OpenClaw 版本** | 2026.2.26 |
| **目标版本** | 2026.3.13 |
| **备份大小** | 17 KB |

---

## 备份文件

- `openclaw-backup-20260320-1326.tar.gz` - **完整配置备份**（推荐使用）

---

## 备份内容

### 核心配置
- `openclaw.json` - 主配置文件（含 API Keys）
- `AGENTS.md`, `SOUL.md`, `USER.md`, `TOOLS.md` 等工作区文件
- 14 个 agent 的模型配置 (`agent-models/*.json`)
- 记忆数据 (`memory/`)
- `plugins-list.txt` - 已安装插件列表
- `skills-list.txt` - 已安装技能列表
- `RESTORE.md` - 恢复指南

### 系统配置
- `openclaw-gateway.service` - Systemd 服务配置
- `paired.json` - 设备配对信息
- `env-vars.txt` - 环境变量（不含 GitHub token）

### 已启用的插件 (6 个)
1. **Dashscope Config** - Dashscope 配置
2. **DingTalk** - 钉钉通道 (3.1.4)
3. **Memory (LanceDB Pro)** - 长期记忆 (1.1.0-beta.9) ⭐
4. **QQ Bot** - QQ 机器人 (1.5.0)
5. **WeCom** - 企业微信 (0.1.3)
6. **Feishu** - 飞书通道 (2026.2.26) ⭐

### 已安装技能 (22 个)
**量化交易**: backtest-expert, fundamental-stock-analysis, market-sentiment-pulse, multi-factor-strategy, quant-trading-cn, trading-sop.md

**记忆学习**: arxiv-skill-learning, learning, memory-lancedb-pro-skill, self-improving

**任务管理**: task-flow, test-generator, code-review-fix

**工具集成**: blogwatcher, find-skills, notion, obsidian, openclaw-github-assistant, openclaw-tavily-search, searxng

**专业分析**: risk-management-specialist, technical-analyst

### 已配置功能

#### 1. memory-lancedb-pro
- Embedding: 阿里云 `text-embedding-v3`
- LLM: 百炼 `qwen3.5-plus`
- Rerank: 阿里云 `gte-rerank`
- Smart Extraction: ✅ 开启
- Auto Capture/Recall: ✅ 开启

#### 2. 14 个 Agent 模型分配
| Agent | 模型 | 思考级别 |
|-------|------|---------|
| main | qwen3.5-plus | high |
| coder | qwen3-coder-plus | high |
| strategy-expert | qwen3-max | high |
| test-expert | qwen3.5-plus | medium |
| doc-manager | kimi-k2.5 | low |
| parameter-evolver | qwen3-max | high |
| backtest-engine | qwen3-max | high |
| data-collector | MiniMax-M2.5 | off |
| factor-miner | qwen3-max | high |
| finance-learner | qwen3-max | high |
| sentiment-analyst | qwen3.5-plus | low |
| ops-monitor | MiniMax-M2.5 | off |
| knowledge-steward | kimi-k2.5 | low |
| master-quant | qwen3-max | high |

#### 3. 所有模型启用 reasoning
- qwen3.5-plus, qwen3-max, qwen3-coder-plus
- MiniMax-M2.5, glm-5, kimi-k2.5

---

## 恢复步骤

### 1. 下载备份
```bash
wget https://github.com/q1960314/openclaw-backup/raw/main/openclaw-backup-20260320-1326.tar.gz
tar -xzf openclaw-backup-20260320-1326.tar.gz
cd openclaw-backup-final
```

### 2. 恢复配置
```bash
# 主配置
cp openclaw.json /home/admin/.openclaw/

# 工作区文件
cp AGENTS.md SOUL.md USER.md TOOLS.md /home/admin/.openclaw/workspace/

# 记忆文件
cp -r memory/* /home/admin/.openclaw/workspace/memory/

# Agent 模型配置
for f in agent-models/*.json; do
  agent_name=$(basename $f .json)
  cp $f /home/admin/.openclaw/agents/$agent_name/agent/models.json
done

# Systemd 服务
cp openclaw-gateway.service ~/.config/systemd/user/
systemctl --user daemon-reload

# 设备配对
cp paired.json /home/admin/.openclaw/devices/

# 环境变量（检查 env-vars.txt，手动添加到 ~/.bashrc 或 systemd 服务）
```

### 3. 重启验证
```bash
systemctl --user restart openclaw-gateway
openclaw gateway status
openclaw memory-pro stats
openclaw channels list
openclaw plugins list
```

---

## 注意事项

1. **API Keys**: 已包含在 `openclaw.json` 和 `env-vars.txt` 中
2. **记忆数据**: LanceDB 数据库会重新创建
3. **会话历史**: 未备份，恢复后是全新会话
4. **版本兼容**: 2026.2.26 → 2026.3.13 应该兼容
5. **GitHub Token**: 未包含在备份中，需要手动设置

---

## 环境变量（需手动恢复）

备份中包含 `env-vars.txt`，包含以下环境变量：
- `TAVILY_API_KEY`
- `OPENCLAW_GATEWAY_TOKEN`
- 其他非敏感环境变量

**GitHub Token 需要手动设置**：
```bash
export GITHUB_TOKEN=your_token_here
export GITHUB_USERNAME=your_username
```

---

**GitHub 仓库**: https://github.com/q1960314/openclaw-backup
**最新备份**: openclaw-backup-20260320-1326.tar.gz (17 KB)
**备份时间**: 2026-03-20 13:26 GMT+8
