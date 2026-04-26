# Claude Code Hooks 配置实战

> 研究日期：2026-04-26  
> 状态：实战配置 + 凯戈模板  
> 研究员：凯戈 + Claude

---

## 一、Hooks 是什么

Hooks 是 Claude Code 的事件系统：当特定事件发生时，自动执行 Shell 命令。

**核心价值**：把 CLAUDE.md 里「Claude 可能忽略」的规则变成「系统强制执行」的检查点。

执行率对比：
- CLAUDE.md 纯文字指令 → ~70%
- Hooks → 100%（Shell 命令，不依赖 Claude 意愿）

---

## 二、配置位置

```
~/.claude/settings.json        # 全局 Hooks（所有项目生效）
<项目>/.claude/settings.json   # 项目级 Hooks（仅当前项目生效）
```

---

## 三、事件类型

| 事件 | 触发时机 | 典型用途 |
|------|----------|----------|
| `PreToolUse` | Claude 调用工具**前** | 拦截危险操作、提前检查 |
| `PostToolUse` | Claude 调用工具**后** | 验证结果、记录日志 |
| `Stop` | Claude **完成回答**后 | 收工提醒、自动推送 |
| `Notification` | Claude 需要用户注意时 | 自定义通知方式 |

---

## 四、settings.json 完整模板（凯戈适用）

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "if echo \"$CLAUDE_TOOL_INPUT\" | python3 -c \"import sys,json; d=json.load(sys.stdin); print(d.get('command',''))\" 2>/dev/null | grep -q 'git push'; then echo '=== git push 前状态确认 ===' && git status; fi"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo '\\n=== 收工提醒 ===' && echo '记得 git push！' && git status 2>/dev/null | head -8"
          }
        ]
      }
    ]
  }
}
```

---

## 五、Hooks 工作原理

### 5.1 matcher（匹配器）

PreToolUse / PostToolUse 支持 matcher 过滤工具类型：

```json
"matcher": "Bash"      // 只匹配 Bash 工具调用
"matcher": "Edit"      // 只匹配文件编辑
"matcher": "Write"     // 只匹配文件写入
// 不写 matcher = 匹配所有工具
```

### 5.2 环境变量

Hook 命令执行时可以访问：

| 变量 | 内容 |
|------|------|
| `$CLAUDE_TOOL_INPUT` | 工具调用的输入参数（JSON 字符串） |
| `$CLAUDE_TOOL_OUTPUT` | 工具调用的输出（PostToolUse） |
| `$CLAUDE_PROJECT_DIR` | 当前项目目录 |

### 5.3 退出码控制

```bash
exit 0   # 正常，Claude 继续执行
exit 1   # Hook 失败，Claude 收到失败信号（不会自动阻止操作）
exit 2   # 阻止操作（PreToolUse 专用，Claude 不会执行该工具调用）
```

---

## 六、常用 Hook 配方

### 6.1 git push 前状态确认

```json
{
  "PreToolUse": [{
    "matcher": "Bash",
    "hooks": [{
      "type": "command",
      "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q 'git push'; then echo '=== push 前确认 ===' && git status && git diff --stat HEAD; fi"
    }]
  }]
}
```

### 6.2 收工提醒（每次 Claude 完成回答）

```json
{
  "Stop": [{
    "hooks": [{
      "type": "command",
      "command": "echo '收工了？记得 git push！' && git status 2>/dev/null | grep -E 'modified|Untracked|ahead' | head -5"
    }]
  }]
}
```

### 6.3 禁止修改特定文件（PreToolUse 拦截）

```json
{
  "PreToolUse": [{
    "matcher": "Edit",
    "hooks": [{
      "type": "command",
      "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q 'settings.json'; then echo '禁止修改 settings.json！需要凯戈确认' && exit 2; fi"
    }]
  }]
}
```

### 6.4 自动追加 dev_log（PostToolUse 在 Write 后触发）

```json
{
  "PostToolUse": [{
    "matcher": "Bash",
    "hooks": [{
      "type": "command",
      "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q 'git commit'; then echo \"[$(date '+%Y-%m-%d %H:%M')] commit 完成\" >> ~/kaige-brain/activity.log; fi"
    }]
  }]
}
```

---

## 七、调试 Hooks

```bash
# 手动测试 Hook 命令（在终端直接跑）
export CLAUDE_TOOL_INPUT='{"command": "git push origin main"}'
bash -c "你的 hook command"

# 查看当前 settings.json
cat ~/.claude/settings.json | python3 -m json.tool
```

---

## 八、凯戈的 Hooks 现状（2026-04-26）

- `PreToolUse`：git push 前自动 git status
- `Stop`：收工时提醒 git push + 显示状态
- **待添加**：禁止修改 CLAUDE.md（需凯戈确认）

---

## 关联词条

- [[overview]] — Claude Code 整体架构
- [[claude-md-patterns]] — CLAUDE.md 与 Hooks 协同
- [[slash-commands]] — 另一种自动化手段
- [[workflow-patterns]] — Hooks 在凯戈工作流中的位置
