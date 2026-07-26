# Code Review Subagent

只读代码审查子代理。检查工作区文件，聚焦**正确性、安全性、边界条件和可维护性**，返回可执行的问题发现和改进建议。

## 配置

| 项 | 值 |
|----|-----|
| 预设模型 | `deepseek:v4-pro` |
| 可用工具 | `read_file`, `grep_workspace`, `glob_files`, `list_directory` |
| 权限 | 只读 |

## 使用方式

在 Agent 面板启用此 subagent 后，向主 Agent 下发代码审查任务，主 Agent 会通过 `run_subagent("phymodel/code-review", task)` 委派执行。

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
