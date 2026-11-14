---
title: "练习 5 - 监视和管理代理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义代理][previous-lesson] |
|:--|

使用 GitHub Copilot 代理模式时，它会自主探索代码库、提出更改、编辑文件并运行命令。这些工作会实时发生在你的计算机上，因此 VS Code 中的 Copilot Chat 会持续显示每次工具调用和文件编辑。你可以在编辑器中查看每个差异、接受或拒绝单项更改，并通过后续提示引导对话、完善或扩展工作。

本练习将学习如何：

- 实时监视无障碍代理的工作。
- 审查并接受建议的文件编辑。
- 在会话过程中引导代理，在高对比度模式之外添加浅色模式切换开关。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义代理正在实现高对比度模式。工作进行期间，设计团队又提出了一个要求：为觉得默认主题太暗的用户添加浅色模式切换开关。无需从头开始新对话，你将引导现有会话扩展功能。

## 监视 Copilot 的进度

Copilot Chat 视图会显示代理执行的每个步骤，包括探索的文件、运行的命令和建议的编辑。每项文件编辑都会以内联方式显示在聊天中，附有差异和 **Keep** / **Undo** 控件。

1. 返回练习 4 中打开的 Copilot Chat 视图。
2. 代理工作时滚动聊天内容，查看每次工具调用。你会看到文件读取、文件编辑，以及它请求运行的终端命令。
3. 代理提出文件编辑时，差异会以内联方式显示：
   - 选择 **Keep** 接受更改。
   - 选择 **Undo** 撤销更改，让代理再次尝试。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看代理在本次会话中修改的完整文件集合。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），代理会暂停并显示 **Allow** 提示。只批准你认可的命令。

5. 等待代理完成高对比度模式的实现。完成时应该会看到总结更改内容的最终消息。

## 引导代理添加浅色模式

高对比度模式已经就绪，现在扩展同一对话，添加浅色模式切换开关。继续使用当前聊天会话，代理会保留之前工作的所有上下文，包括修改的组件、样式方法和本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 代理将从现有更改继续探索，并提出添加浅色模式的修改。
3. 每项新编辑出现时都要审查，就像处理高对比度模式时一样。对每个差异选择 **Keep** 或 **Undo**。
4. 如果代理的方向不合适（例如，它尝试以你不喜欢的方式重构切换 UI），可以选择聊天输入框旁的 **Stop** 按钮停止它，然后发送澄清后的后续消息。

> [!TIP]
> 在现有会话中引导代理，比从头开始更快。可以使用后续提示请求调整（“简化 CSS，改用 CSS 变量而不是重复选择器”）、纠正方向（“不要添加第三个切换开关，把浅色模式整合进现有设置面板”）或扩展范围（“同时为两个开关添加键盘快捷键”）。

## 审查工作文件

提交前，快速检查代理修改的全部内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 查看已更改文件的完整列表。应能看到 Astro 组件、样式和相关测试的更新。
3. 打开几个已更改的文件并查看差异。确认其中体现了自定义代理要求的无障碍实践，包括 ARIA 属性、键盘导航、语义化 HTML 和本地存储。
4. 满意后，在 Source Control 面板中暂存并提交更改。下一课将发布这些更改。

## 总结和后续步骤

你使用 Copilot Chat 代理模式监视并引导正在进行的会话：逐项审查文件编辑、接受或拒绝更改，并通过后续提示扩展工作，同时保留上下文。

本课介绍了：

- 实时监视代理工作。
- 接受或拒绝单项文件编辑。
- 在会话进行中使用后续提示引导代理。

在[下一课][next-lesson]中，你将把无障碍更改作为拉取请求发布，并审查本地自定义代理在整个实验中的工作。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用代理模式][agent-mode]

---

| [← 上一课：自定义代理][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
