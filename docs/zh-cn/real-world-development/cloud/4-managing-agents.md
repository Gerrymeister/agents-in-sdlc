---
title: "练习 4 - 监视和管理代理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前两节练习中，你让 Copilot cloud agent 承担了三个独立任务，用于改善用户体验和添加功能。尽管 cloud agent 专为异步自主工作而设计，监视任务仍然很重要。

你可以使用多种工具管理分配给 cloud agent 的任务，包括 GitHub.com 上的[代理页面][agents-page]。在这个任务控制中心，可以查看所有创建了未关闭拉取请求（PR）的代理任务、查看执行的操作，甚至引导进行中的会话。

本课将学习如何：

- 使用代理页面监视 cloud agent 任务。
- 在会话进行中引导代理添加功能。

## 场景

分配实现高对比度模式的任务后，团队意识到现在也适合添加浅色模式。既然正在修改网站样式并添加切换功能，顺便实现浅色模式很合理。现在引导代理同时添加浅色和高对比度模式。

## 查看 Copilot cloud agent 任务

查看当前分配给 Copilot cloud agent 的任务状态。

1. 打开 [Copilot 代理页面](https://github.com/copilot/agents)。
2. 查看主面板和左侧面板中的任务列表。应能看到分配给 Copilot 的任务，包括：
   - 更新代码文档。
   - 生成用于修改游戏的 API。
   - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的工作，例如：
   - 从存储库检出代码。
   - 为 Copilot 创建工作环境。
   - 设置 MCP 服务器。
   - 执行完成分配任务所需的各种步骤。

> [!NOTE]
> 列出的具体步骤会根据 Copilot 的工作进度和采用的方法而变化。

4. 同时留意右侧的拉取请求（PR）面板。你可以在其中查看 PR 和更改的文件，以便进一步监视。

## 引导 cloud agent

查看当前活动任务后，要求 Copilot 在实现高对比度模式的同时添加浅色模式切换开关。

1. 选择与添加高对比度模式相关的会话。具体标题会因 Copilot 选用的名称和当前工作状态而异。

    ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 监视会话几分钟，直到它显示设置已完成并开始工作。出现类似下方的消息时，就表示已进入此阶段。
3. 在 **Steer active session while Copilot is working** 对话框中，输入以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![代理页面中 Copilot cloud agent 任务的截图，其中突出显示了 steer active session while copilot is working 对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 留意 Copilot 如何确认收到提示并将其加入工作流程。

## 等待 Copilot 完成工作

和之前一样，Copilot 会继续处理更新后的任务！收到消息时，它会先完成当前步骤，然后将新请求加入工作流程。

这可能需要几分钟，可以趁此机会暂停一下，回顾目前学到和探索的内容。

## 总结和后续步骤

本课介绍了 Copilot 代理页面，它是监视和引导 GitHub Copilot cloud agent 任务的中心位置。你可以跟踪所有正在进行和已完成的任务、审查已执行的工作，甚至重定向进行中的任务来调整范围或补充指导。

本课介绍了以下内容：

- 使用任务控制中心和代理页面监视 cloud agent 任务。
- 重定向进行中的会话以请求添加功能。

Copilot 正在完成无障碍功能，接下来将[迭代 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 代理页面][agents-page]
- [关于自定义代理][custom-agents]

---

| [← 上一课：自定义代理][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
