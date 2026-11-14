---
slug: zh-cn/real-world-development/cloud
title: "GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

**[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent)** 可在云端异步处理任务。你在 GitHub 上分配工作后，cloud agent 会在后台接手任务、探索存储库、进行更改并创建拉取请求，让你可以继续处理其他事情。

本系列练习将添加 cloud agent 会遵循的自定义说明，然后分配 GitHub 问题并让它实现功能。你将审查并使用自定义代理来引导它的工作方式，在代理页面监视并引导会话，最后审查它创建的拉取请求并迭代优化结果。

## 练习

| 练习 | 主题 | 说明 |
|----------|-------|-------------|
| [0. 准备工作][ex0] | 设置 | 创建存储库和 codespace |
| [1. 自定义说明][ex1] | 上下文 | 添加 cloud agent 会遵循的自定义说明 |
| [2. Cloud Agent][ex2] | 异步代理 | 将问题分配给 Copilot cloud agent |
| [3. 自定义代理][ex3] | 专用代理 | 查看并使用自定义代理 |
| [4. 管理代理][ex4] | 监视 | 监视并引导代理会话 |
| [5. 迭代][ex5] | 审查 | 审查 PR、迭代改进 Copilot 的工作并选择后续步骤 |

## 先决条件

参加本工作坊前，请确认你具备以下条件：

- [ ] 拥有有效的 **Copilot Student、Pro、Pro+、Business 或 Enterprise** 计划的 GitHub 帐户
- [ ] 已为你的帐户或组织启用 Copilot cloud agent

> [!TIP]
> 没有付费计划？通过 [GitHub Education][callout-student-plan-education] 验证身份的学生可免费使用 GitHub Copilot。**Copilot Student** 计划包含本工作坊使用的代理、MCP、代码审查和 Copilot CLI 功能，因此可使用它完成所有环境中的练习。

[callout-student-plan-education]: https://github.com/education/students

> [!NOTE]
> Copilot cloud agent 适用于 **Copilot Student、Pro、Pro+、Business 和 Enterprise**，除 Copilot Free 外的所有计划均包含此功能。Copilot Business 或 Enterprise 组织需要管理员启用该功能。

> [!NOTE]
> 本系列练习不涉及 MCP。要了解如何在 Copilot 中使用 MCP 服务器，请参阅 [CLI 系列][cli-workshop]或 [VS Code 系列][vscode-workshop]。

[cli-workshop]: ../cli/
[vscode-workshop]: ../vscode/

## 开始

**[从练习 0：准备工作开始 →][ex0]**

[ex0]: 0-prerequisites/
[ex1]: 1-custom-instructions/
[ex2]: 2-cloud-agent/
[ex3]: 3-custom-agents/
[ex4]: 4-managing-agents/
[ex5]: 5-iterating/
