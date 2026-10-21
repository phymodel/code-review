# Code Review Subagent

只读代码审查子代理。检查工作区文件，聚焦**正确性、安全性、边界条件和可维护性**，返回可执行的问题发现和改进建议。

## 配置

| 项 | 值 |
|----|-----|
| 预设模型 | `auto`（不固定模型，主 Agent 每次委派时选择） |
| 可用工具 | `read_file`, `grep_workspace`, `glob_files`, `list_directory` |
| 权限 | 只读 |

## 使用方式

在 Agent 面板启用此 subagent 后，向主 Agent 下发代码审查任务，主 Agent 会通过
`run_subagent("<prefix>/code-review", task, model="<模型名>")` 委派执行。

## 模型选择（`preset_id: auto`）

本包的 `preset_id` 是 `auto`：**不固定模型**，由主 Agent 在每次委派时按任务难度选择并写进 `model` 参数。

| 场景 | 选择 |
|------|------|
| 大 diff、并发 / 安全 / 数据迁移相关代码、架构级问题、第一轮结论可疑时的复核 | 「Available remote large models」里推理最强的那一档 |
| 单文件、小 diff、命名 / 风格 / 拼写清理、已知问题的复查 | 同一列表里最快最便宜的一档 |
| 拿不准 | 先用快模型，发现结论偏薄或漏掉风险再升级 |

模型名必须来自主 Agent 看到的「Available remote large models」列表（即侧边栏「Models」里已填 API Key 且勾选启用的条目）：
名字不认识或未配置会被后端直接拒绝，委派根本不会发起，也不会花掉任何 token。

如果把 `preset_id` 改回某个具体模型名，则该模型被固定，委派时传入的 `model` 会被忽略（主 Agent 会在结果的
`model_note` 里收到说明）——这是给需要控制成本的场景准备的。

## 输出

按以下结构返回审查结果：

1. **Summary**（1–3 句概述）
2. **Issues**（按严重程度分级：critical / major / minor）
3. **Optional suggestions**（无直接文件编辑）

## 适用场景

- PR / MR 提交前的自动化代码审查
- 安全漏洞静态扫描（密钥泄露、注入风险等）
- 代码质量门禁（边界条件、异常处理、可维护性检查）
- 重构前后的差异审查
