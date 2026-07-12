# 通用 Coding Agent 配置

获得一个能够开箱即用的通用配置的能力，这里以我自己的 Claude 举例



### 一、CLAUDE.md 配置

1.官网规范：https://code.claude.com/docs/zh-CN/claude-directory

2.分级文件夹路径 - 个人或团队配置指南和最佳实践：https://www.cnblogs.com/yoyo1216/p/19755828

3.高星范例：https://github.com/multica-ai/andrej-karpathy-skills



### 二、Skills

1.Skills 的分类：

- Personal Skills：个人技能，你的所有项目都可以用上的 Skills 哦，位于 `~/.claude/skills/` 目录下；
- Project Skills：项目技能，项目技能仅对项目生效，方便团队共享，位于每个项目中的 `.claude/skills/` 目录下；
- Plugin Skills：插件技能，插件也能捆绑一些 Skills，安装后就能直接用，用法和个人以及项目 Skills 是一样的

2.Skill Hub 技能广场：https://skillhub.cn/

3.自用技能：参考 `~/skills `

4.高星技能范例：

Superpowers：https://github.com/obra/superpowers

Self-Improving Agent：https://skillhub.cn/skills/self-improving-agent



### 三、MCP

1.配置位置：`C:\Users\XXX\.claude.json`

2.MCP 广场：https://skillhub.cn/mcp



### 四、Hooks

1.适用场景：

- 每次修改代码后，自动运行 lint 和格式化
- 执行危险命令前，自动检查当前分支
- 工具执行失败后，自动发送通知

2.配置示例：

```
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/validate-bash.sh"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

3.脚本示例：

```
#!/bin/bash
# .claude/hooks/quality-check.sh

# 读取工具输入
INPUT=$(cat)
FILE_PATH=$(echo"$INPUT" | jq -r '.tool_input.file_path')

# 只检查 TypeScript 文件
if [[ "$FILE_PATH" =~ \.(ts|tsx)$ ]]; then
# 运行 ESLint
if ! npx eslint "$FILE_PATH" --fix; then
    jq -n '{
      hookSpecificOutput: {
        hookEventName: "PostToolUse",
        decision: "block",
        reason: "ESLint found issues that could not be auto-fixed"
      }
    }'
    exit 0
fi
fi

exit 0
```



### 五、Memory

Coding Agent 的记忆场景选型，采用默认记忆配置即可:

- 如果有长期记忆的需求，可以选用热门的记忆框架例如 Mem0；

- 或参考 Hermes Agent 的记忆设计能够自我进化的 渐进性成长记忆。



### 六 、通用 Prompt

参考  `~/prompts `
