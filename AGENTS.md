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
        <ITEM>命令入口：优先查项目根目录的 `重要命令.txt`、`scripts/`、`docs/` 目录。</ITEM>
      </CHECKLIST>
    </BEFORE_WORK>

    <BEST_PRACTICE>
      <RULE>持续进行架构性改动和重构式开发，使用简洁、一劳永逸的方式编码，让系统不可能出错。</RULE>
      <BAD_EXAMPLE>
        以下是典型的非架构性改动，应避免使用：
        1. 增加检查/校验：到处加 if data is None 检查，而非用类型系统保证非空
        2. 增加重试/兜底：加 retry 3 次、返回默认值，而非解决不稳定的根因
        3. 增加日志/监控：加更多 logger.info 排查问题，而非让数据流天然可追溯
        4. 增加锁/同步：加 mutex/lock 防并发冲突，而非消除共享状态
        5. 增加文档/注释：写 WARNING 注释警告 API 误用，而非让 API 设计上不可能误用
        6. 增加异常捕获/吞错：try-except pass 吞掉异常继续跑，而非让错误不发生或立即暴露
      </BAD_EXAMPLE>
    </BEST_PRACTICE>

    <BUSINESS_LOGIC_PARAMETERIZATION>
      <DEFINITION>
        业务逻辑参数化：AI 通过增加参数/开关/配置项来表达业务逻辑的变化，而非直接修改代码。
        判断标准：参数是否影响业务结果（输出不同、行为不同）。影响业务结果 = 业务逻辑参数。
      </DEFINITION>

      <RULE>禁止用参数表达业务逻辑变化。当用户要求改变业务逻辑时，直接修改代码；当用户要求删除功能时，删除相关代码。</RULE>

      <PROHIBITED>
        以下类型的参数禁止引入：
        - 行为开关：--force, --legacy, --skip-check, --enable-feature-x
        - 模式切换：--v1-mode, --use-new-binding, --compatibility-mode
        - 功能开关：enable_feature_a=true, disable_validation=true
        - 任何用于绕过正常流程或兼容旧行为的参数
      </PROHIBITED>

      <ALLOWED>
        以下类型的参数允许存在：
        - 用户输入：--input-file, --output-dir（用户必须指定的输入）
        - 调试/日志：--verbose, --quiet（不影响业务结果）
        - 性能调优：--parallel=4, --batch-size=100
        - 输出格式：--format=json（当不影响下游业务结果时）
      </ALLOWED>

      <HANDLING>
        当 AI 倾向于增加业务逻辑参数时：
        1. 默认假设无兼容性约束，直接修改代码
        2. 默认假设有测试覆盖，改动后测试会暴露问题
        3. 仅当用户指令有歧义或涉及业务逻辑决策时，询问用户
        4. 当代码库存在多种不一致模式时，询问用户统一方向
        5. 破坏性改变应升级主版本号
      </HANDLING>

      <SCOPE>本规则适用于：CLI 命令行参数、函数/方法参数、配置文件选项、环境变量。</SCOPE>
    </BUSINESS_LOGIC_PARAMETERIZATION>

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

	<RULE>分阶段方案结构化（遵循先查后建）</RULE>
	<PROCEDURE>
	  当方案确认分阶段实施时：
	  1. 为整体方案创建 epic：bd create -t epic --title="..."
	  2. 为每阶段创建 issue，标题格式：「阶段 N: 描述」
	  3. 所有阶段 issue 关联 epic：bd dep add <阶段-issue> <epic>
	  4. 阶段间依赖：bd dep add <后续阶段> <前置阶段>
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
