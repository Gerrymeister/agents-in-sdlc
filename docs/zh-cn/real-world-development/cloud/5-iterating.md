---
title: "练习 5：迭代优化 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理代理][previous-lesson] |
|:--|

## 审查工作

在整个实验过程中，你与 GitHub Copilot 协作完成了多个改善用户体验和添加功能的任务。你让 Copilot 为代码添加文档、构建供设计团队迭代的相关游戏功能，以及实现包含高对比度和浅色模式切换开关的无障碍功能。现在来检查代码更改，必要时向 Copilot 提供反馈，改进它的工作。

### 场景

正如前面多次强调的，引入生成式 AI 不会改变软件设计和 DevOps 的基本原则。我们始终需要审查生成的代码，并遵循常规 DevOps 流程。现在先审查 GitHub Copilot 生成的文档、相关游戏功能和无障碍功能，再为团队其他成员启用审查。

## 安全和 GitHub Copilot cloud agent

Copilot cloud agent 会异步且无人监督地执行任务，因此设有一些安全限制：

- Copilot 只能读取存储库，并且只能写入它将用于提交代码的分支。
- Cloud agent 在 GitHub Actions 中运行，并创建独立的临时环境进行工作。
- GitHub Actions 工作流必须经过人工批准后才能运行。
- [默认情况下，外部资源访问受限](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，MCP 服务器也包括在内。

## 审查生成的文档

先查看 GitHub Copilot cloud agent 创建的第一个拉取请求（PR），其中包含代码文档。通过 GitHub.com 上的标准 PR 界面进行审查。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为 Copilot 默认只能有限访问外部资源，包括外部 MCP 服务器。如果需要，可以[自定义或禁用 Copilot cloud agent 的防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开拉取请求列表。
3. 打开标题类似 **Add missing documentation** 或更完整描述的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求中会包含 **[WIP]** 标记。请等待 Copilot 完成工作。这可能需要几分钟，可以休息一下，也可以回顾目前学到的内容。

4. 拉取请求准备就绪后，选择 **Files changed** 选项卡并审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看新添加的 TSDoc 文档注释和其他文档。具体更改会有所不同。

    检查更改时，留意 TSDoc 文档注释、TypeScript 约定和文件注释头。这些内容来自你在练习 2 开始时查看的自定义说明文件。

6. 审查完更新并确认符合预期后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流指示器。
8. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 你应该会看到工作流已加入拉取请求的检查列表。如果一切正常，单体 Astro 应用的项目检查将通过。这可能需要几分钟。

## 请求 GitHub Copilot 进行更改

与 Copilot 协作并非单向过程。你可以像 @ 团队成员一样，在拉取请求评论或代码行内评论中提及 Copilot。Copilot 会读取评论，并启动另一个会话来处理请求。由于结果具有非确定性，我们无法提供固定的提示文字。可以要求 Copilot 更新的内容包括：

- 为每个代码文件开头添加简短说明文件用途的注释头。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用结构。

1. 添加评论，请求 Copilot 更改生成的文档，并像提及其他用户一样标记 **@copilot**。可以使用上面的一项建议，也可以提出其他希望添加到代码库文档中的要求。
2. 选择 **View Session**，查看 Copilot 执行的工作。留意 Copilot 如何启动新会话以进行更新。
3. 可以选择 **Back to pull request** 返回拉取请求。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，拉取请求中应会出现新的提交。
5. 选择 **Files changed** 选项卡审查更改。

可以继续迭代，直到满意为止。满意后，可以将 PR 从草稿转换为准备审查状态，然后合并到 `main` 分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

返回 Copilot 创建的 PR，解决游戏详情页显示相关游戏的问题。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Show related games on the game details page** 或更完整描述的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查完更新并确认符合预期后，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有等待批准的工作流指示器。
7. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. 你应该会看到工作流已加入拉取请求的检查列表。如果一切正常，单体 Astro 应用的项目检查将通过。这可能需要几分钟。
9. **可选：**也可以在 Codespace 中切换到此分支，手动测试相关游戏功能。打开 Codespace 和终端，运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已构建相关游戏功能！与之前一样，你可以通过迭代让 Copilot cloud agent 继续修改。例如，可以要求 Copilot 调整显示的相关游戏数量，或添加文件注释头和 TSDoc 文档注释（请记住，分配此任务时，你还没有更新自定义说明！）。和之前一样，可以在 **Conversation** 选项卡中添加评论，Copilot 会读取评论并启动新会话。

## 审查无障碍功能

最后，审查通过无障碍自定义代理实现的功能。此 PR 应同时包含练习 3 中分配的高对比度模式，以及练习 4 中在任务控制中心提出的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Add high contrast mode to website** 或更完整描述的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求中会包含 **[WIP]** 标记。请等待 Copilot 完成工作，这可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查实现，尤其留意：
   - 用于切换模式的 UI 组件。
   - 用于保存用户偏好的本地存储。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 用于管理模式切换的 JavaScript/TypeScript 代码。

6. 审查完更新并确认符合预期后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流指示器。
8. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 你应该会看到工作流已加入拉取请求的检查列表。如果一切正常，单体 Astro 应用的项目检查将通过。这可能需要几分钟。
10. **可选：**可以在 Codespace 中切换到此分支，手动测试无障碍功能。打开 Codespace 和终端，运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式切换开关，确认功能符合预期并且在页面重新加载后仍然生效。

请注意，无障碍自定义代理如何引导 Copilot 遵循无障碍最佳实践实现这些功能。如果发现无障碍问题或改进点，可以像处理前面几个 PR 一样，在评论中标记 **@copilot** 请求更新。

## 可选练习 — 继续委派任务

Cloud agent 最适合处理真实待办事项，让你能专注于其他工作。继续练习这种方式：在存储库中创建更多问题并分配给 Copilot。可以尝试：

- 在游戏详情页创建支持者兴趣表单。
- 为游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成 Cloud agent 系列练习。在这些课程中，你：

- **检查了此存储库附带的自定义说明文件**，以便之后观察它们对 cloud agent 输出的影响。
- **将问题分配给 Copilot cloud agent**，并观察它设置环境、制定计划和异步执行任务。
- **创建并使用自定义代理**实现无障碍功能，包括高对比度和浅色模式切换开关。
- **使用 Copilot 代理页面作为任务控制中心**，监视并引导进行中的无障碍会话。
- **审查并迭代 cloud agent 的拉取请求**，通过标记 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

你已完成 Cloud agent 系列练习。想继续探索的话，还可以学习其他系列，拓展本系列中实践的内容：

- 🖥️ **[VS Code 系列](../../vscode/)** — 直接在 IDE 中探索 Copilot Chat 代理模式和 MCP 集成。
- 💻 **[CLI 系列](../../cli/)** — 从终端交付两个经过审查的更改，使用 Plan 和 Autopilot 模式、代理技能、自定义 QA 代理、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云端代理桥接。

在自己的存储库中，可以尝试以下后续任务：

- 将重构或测试覆盖率问题分配给 cloud agent。
- 为其他领域创建自定义代理。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 代理][copilot-agents]
- [将 GitHub 问题分配给 Copilot][assign-issue]
- [Copilot cloud agent 设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot cloud agent 防火墙][agent-firewall]

---

| [← 上一课：管理代理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
