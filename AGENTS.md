<!-- claudemd:begin -->
<AGENTS>
  <LANGUAGE>
    默认中文沟通；代码标识符保留原文。
  </LANGUAGE>

  <SHELL>
    <DEFAULT>当前环境默认 shell 通常是 zsh；functions.shell_command 会按默认 shell 解析命令。</DEFAULT>
    <RULE>不要默认当成 bash；需要 bash 语法/行为时，显式使用：bash -lc '...'</RULE>
  </SHELL>
 
  <WORKFLOW>
    <BEFORE_WORK>
      <CHECKLIST>
        <ITEM>命令入口：优先查 `重要命令.txt`；以及其他各种编程中常见的记录使用方法、guide的文档。</ITEM>
      </CHECKLIST>
    </BEFORE_WORK>

    <BEST_PRACTICE>
      <RULE>持续进行架构性改动和重构式开发，使用简洁、一劳永逸的方式编码，让系统不可能出错。</RULE>
      <BAD_EXAMPLE>
        以下是典型的非架构性改动，应避免使用：
        1. 增加参数/开关：加 --force、--legacy、--skip-check 让用户绕过问题，而非让系统自动正确处理
        2. 增加检查/校验：到处加 if data is None 检查，而非用类型系统保证非空
        3. 增加重试/兜底：加 retry 3 次、返回默认值，而非解决不稳定的根因
        4. 增加日志/监控：加更多 logger.info 排查问题，而非让数据流天然可追溯
        5. 增加锁/同步：加 mutex/lock 防并发冲突，而非消除共享状态
        6. 增加配置项：加 enable_legacy_mode、use_new_binding 开关，而非删除旧路径统一行为
        7. 增加文档/注释：写 WARNING 注释警告 API 误用，而非让 API 设计上不可能误用
        8. 增加异常捕获/吞错：try-except pass 吞掉异常继续跑，而非让错误不发生或立即暴露
      </BAD_EXAMPLE>
    </BEST_PRACTICE>

    <FALLBACK_POLICY>
      <RULE>术语约束：本项目将“兜底/降级/重试/默认值/吞错/缓存旧数据/弱一致替代”等统称为 fallback；将“直接报错（fail-loud）作为默认选择。</RULE>
      <RULE>禁止引入/启用fallback。</RULE>
      <RULE>默认 fail-loud（开发/测试/生产一致）。</RULE>
    </FALLBACK_POLICY>
  </WORKFLOW>

  <TESTING>
    <PYTEST>
      <RULE>禁止使用 -q/--quiet</RULE>
      <VENV>先进入虚拟环境（优先 .venv，其次 venv）</VENV>
      <RUN>source .venv/bin/activate 2>/dev/null || source venv/bin/activate 2>/dev/null && pytest -ra --tb=line</RUN>
    </PYTEST>
  </TESTING>

  <Beads-BD-bd-Landing the Plane (Session Completion)-纠正>
	只要工作过程中出现任何未在本次改动中彻底闭环的事项——无论是本来可以在本次顺手完整解决但未做，还是现在不处理也不会自行消失、后续必然需要处理/决策/对齐——都必须记录在 bd issue 中（影响可用性只决定优先级，不决定是否建）。

	<RULE>先查后建（防止重复）</RULE>
	<PROCEDURE>
	  1. 搜索现有 issue：bd list --status=open（用关键词过滤）、bd show <id>
	  2. 判断相似性：问题描述、根因、受影响范围是否一致
	  3. 相似 → bd update <id> 追加信息；不相似 → bd create 新建
	</PROCEDURE>
  </Beads-BD-bd-Landing the Plane (Session Completion)-纠正>

</AGENTS>
<!-- claudemd:end -->


<!-- BEGIN BEADS INTEGRATION -->
## Issue Tracking with bd (beads)

**IMPORTANT**: This project uses **bd (beads)** for ALL issue tracking. Do NOT use markdown TODOs, task lists, or other tracking methods.

### Why bd?

- Dependency-aware: Track blockers and relationships between issues
- Git-friendly: Auto-syncs to JSONL for version control
- Agent-optimized: JSON output, ready work detection, discovered-from links
- Prevents duplicate tracking systems and confusion

### Quick Start

**Check for ready work:**

```bash
bd ready --json
```

**Create new issues:**

```bash
bd create "Issue title" --description="Detailed context" -t bug|feature|task -p 0-4 --json
bd create "Issue title" --description="What this issue is about" -p 1 --deps discovered-from:bd-123 --json
```

**Claim and update:**

```bash
bd update bd-42 --status in_progress --json
bd update bd-42 --priority 1 --json
```

**Complete work:**

```bash
bd close bd-42 --reason "Completed" --json
```

### Issue Types

- `bug` - Something broken
- `feature` - New functionality
- `task` - Work item (tests, docs, refactoring)
- `epic` - Large feature with subtasks
- `chore` - Maintenance (dependencies, tooling)

### Priorities

- `0` - Critical (security, data loss, broken builds)
- `1` - High (major features, important bugs)
- `2` - Medium (default, nice-to-have)
- `3` - Low (polish, optimization)
- `4` - Backlog (future ideas)

### Workflow for AI Agents

1. **Check ready work**: `bd ready` shows unblocked issues
2. **Claim your task**: `bd update <id> --status in_progress`
3. **Work on it**: Implement, test, document
4. **Discover new work?** Create linked issue:
   - `bd create "Found bug" --description="Details about what was found" -p 1 --deps discovered-from:<parent-id>`
5. **Complete**: `bd close <id> --reason "Done"`

### Auto-Sync

bd automatically syncs with git:

- Exports to `.beads/issues.jsonl` after changes (5s debounce)
- Imports from JSONL when newer (e.g., after `git pull`)
- No manual export/import needed!

### Important Rules

- ✅ Use bd for ALL task tracking
- ✅ Always use `--json` flag for programmatic use
- ✅ Link discovered work with `discovered-from` dependencies
- ✅ Check `bd ready` before asking "what should I work on?"
- ❌ Do NOT create markdown TODO lists
- ❌ Do NOT use external issue trackers
- ❌ Do NOT duplicate tracking systems

For more details, see README.md and docs/QUICKSTART.md.

<!-- END BEADS INTEGRATION -->
