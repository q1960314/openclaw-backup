# OpenClaw 配置备份

## 备份信息

| 项目 | 值 |
|------|-----|
| **备份时间** | 2026-03-20 13:17 GMT+8 |
| **OpenClaw 版本** | 2026.2.26 |
| **目标版本** | 2026.3.13 |
| **备份大小** | 13.7 KB |

---

## 备份文件

- `openclaw-backup-20260320-1317.tar.gz` - 完整配置备份

---

## 备份内容

### 核心配置
- `openclaw.json` - 主配置文件
- `AGENTS.md`, `SOUL.md`, `USER.md`, `TOOLS.md` 等工作区文件
- 14 个 agent 的模型配置 (`agent-models/*.json`)
- 记忆数据 (`memory/`)

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
wget https://github.com/q1960314/openclaw-backup/raw/main/openclaw-backup-20260320-1317.tar.gz
tar -xzf openclaw-backup-20260320-1317.tar.gz
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
```

### 3. 重启验证
```bash
openclaw gateway restart
openclaw memory-pro stats
openclaw channels list
```

---

## 注意事项

1. **API Keys**: 已包含在 `openclaw.json` 中
2. **记忆数据**: LanceDB 数据库会重新创建
3. **会话历史**: 未备份，恢复后是全新会话
4. **版本兼容**: 2026.2.26 → 2026.3.13 应该兼容

---

**GitHub 仓库**: https://github.com/q1960314/openclaw-backup
**备份时间**: 2026-03-20 13:17 GMT+8
