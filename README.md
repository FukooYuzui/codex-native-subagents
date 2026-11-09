# Codex 原生子代理配置示例

这是一份个人本地配置示例：主代理负责理解需求、规划、派单、检查证据和最终答复；普通问答由主代理回答。文件搜索、编辑、构建和测试等工具实操默认委派，即使任务简单也实际派出一个子代理。配置曾在个人环境验证实际路由，但不保证所有客户端、版本或账户均可用，也不保证节省额度。

## 角色和调度

| 角色 | 模型 / 推理强度 | 职责 |
| --- | --- | --- |
| Explorer | `gpt-6-luna` / `medium` | 有边界的只读调查 |
| Worker | `gpt-6.1-terra` / `medium` | 按文件所有权实施和验证 |
| Reviewer | `gpt-6.1-terra` / `high` | 有风险时独立审查 |
| Aesthetic | `gpt-6-astra` / `high` | 配色、排版、视觉风格和视觉复核 |

个人环境的主模型为 Astra / `ultra`，这是示例，合并时请保留自己的主模型默认设置。子代理默认模型为 Terra / `medium`。四种角色最多同时运行三个，实际并发还受运行环境限制。每次派单应写明目标、文件所有权、验收和停止条件；重叠写入串行，子代理不再派生代理，每项任务最多初次尝试加一次有诊断的修复。验收依据实际证据。Explorer 和 Reviewer 的只读要求是指令约束，不是独立沙箱；子代理继承父级的 provider、认证和权限，不绕过审批。

派发默认使用 `fork_turns="none"` 和简短任务说明。仅当调用工具实际列出某个角色时按该角色派发；若没有原生列出 `aesthetic`，则以 `default` 角色显式指定 `model="gpt-6-astra"`、`reasoning_effort="high"`，并在简短说明中写明视觉职责。若旧客户端的派发工具没有 `agent_type` 参数，则按对应角色显式传入模型、推理强度和 `fork_turns="none"`，并在任务说明中写明角色职责。具体参数以客户端实际支持为准。角色文件中的模型与强度优先于显式派发值和 `[agents]` 默认值；修改模型时需同步角色文件与全局路由文字。各账户须自行验证模型及强度是否可用，无法使用时应报告并重新选择已验证的替代方案。Aesthetic 的 `high` 是个人初始选值，并非官方推荐。

Aesthetic 可处理 PPT、网页、文档和界面的视觉设计与复核。混合设计任务采用 Astra 提出视觉方案、Terra 实施、Astra 审美复核的顺序；从截图读取事实不属于审美任务。Astra 角色本身不指定图像生成模型。

## 手工合并

示例不自动安装，也不会覆盖你的配置。Windows 的 PowerShell 与 macOS/Linux 的终端均可使用 `~/.codex` 表示当前用户目录；实际位置以你的 Codex 配置为准。

1. 在本机先为 `~/.codex/config.toml`、`~/.codex/AGENTS.md` 和即将修改的 `~/.codex/agents/` 同名文件建立带时间戳的备份，例如复制到自己的 `<备份目录>/2026-11-10-120000/`。不存在的文件记录为原本不存在。保留现有主模型、provider、密钥、权限和审批设置。
2. 将 [config.agents.toml](examples/config.agents.toml) 中的三个字段手工合并进 `~/.codex/config.toml` 的现有 `[agents]` 表；没有该表才新建。不要用片段覆盖整个配置，也不要重复创建同名表。
3. 将 [角色示例](examples/agents/) 按需合并至 `~/.codex/agents/`。同名文件已存在时先备份并比较差异，再决定合并；不要盲目覆盖已有内容。
4. 将 [AGENTS.md 示例](examples/AGENTS.md) 中受管块合并到 `~/.codex/AGENTS.md`。已有同名 BEGIN/END 块时只替换该块，不重复追加，保留块外指令。
5. 开始一个新会话，确认配置被加载，再用一个有明确验收的简单任务实际派发，检查观察到的角色、模型、返回结果及并发行为。配置能读取或角色描述可发现，并不等于真实派发成功；旧会话不一定重新加载。

回滚时关闭或结束相关会话，按备份恢复修改过的文件；若某文件原本不存在，则移除本次新建文件。开启新会话后重新验证。配置层级及版本变化可能影响行为，遇到问题请核对当前官方文档。

## 测量与费用

Token 用量、Credits 和订阅包含额度是不同概念，不能直接相互当作费用或节省比例。多代理各自执行可能增加 token；当前没有已证实的节省结果。若自行比较，使用相同任务和验收标准，记录所有尝试，并单独核算可获得的用量；不要把累积快照重复相加。价格和账户额度以实际服务为准。

## 官方参考

以下链接核对日期：2026-11-10。

- [Subagents：自定义角色与优先级](https://learn.chatgpt.com/docs/agent-configuration/subagents)
- [配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
- [ChatGPT 定价与额度](https://learn.chatgpt.com/docs/pricing)
- [OpenAI API 定价](https://developers.openai.com/api/docs/pricing)
