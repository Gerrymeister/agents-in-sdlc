---
title: "练习 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

几乎所有组织都会遇到技术债务问题，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot cloud agent 可自主执行代码更新和功能添加等任务。完成后，它会创建一个草稿 PR，供开发者审查。这样可以将繁琐任务交给代理，加快开发流程，让开发者专注于更重要的工作。

本课将使用 Copilot cloud agent 了解：

- 如何自定义代码生成环境。
- 如何确保操作安全。
- 明确限定问题范围的重要性。
- 如何将问题分配给 Copilot。

## 场景

Tailspin Toys 有一些待处理的技术债务。最初受雇开发网站的承包商没有留下完善的文档，事实上，网站完全缺少文档。首先，团队希望为应用中的所有导出函数添加 TSDoc 文档注释。

此外，设计团队准备改善游戏发现体验。他们希望在每款游戏的详情页显示相关游戏（同类别的其他游戏），让用户可以继续浏览。他们暂时不需要完善的实现，只需要能用于 UX 验收测试的功能。目前这项工作受其他事项阻塞，但其他问题的优先级更高。

这两项任务都可能被不断降低优先级，因此很适合交给 Copilot cloud agent。它可以异步处理任务，让开发者先做其他工作，之后再回来审查结果并确认一切符合预期。

## 了解 GitHub Copilot cloud agent

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) 可以像人类开发者一样在后台执行任务。可以通过多种方式向它分配工作，例如[将 GitHub 问题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配后，Copilot 会创建草稿拉取请求来跟踪进度、设置工作环境并开始处理任务。你可以在 Copilot 会话进行中或完成后查看详情。解决方案准备好供审查时，它会在拉取请求中通知你。

## 明确限定说明和任务范围的重要性

GitHub Copilot 没有魔法，不能让你跳过思考。即使是看似简单的操作，深入分析后也会发现其复杂性，因此[为 Copilot cloud agent 合理限定任务范围][cloud-agent-best-practices]非常重要。应将它视为 AI 结对程序员：分阶段工作、学习、尝试并根据结果调整。引入生成式 AI 并不会改变软件开发的基本原则。

## 本存储库中的自定义说明

前面的练习介绍了自定义说明及其如何引导 Copilot。如果你已经完成自定义说明练习，就已看到 `.github/copilot-instructions.md` 的作用；否则，现在可以快速查看。将工作分配给 Copilot cloud agent 前，先只读查看此存储库中已有的说明文件，以便稍后观察它们的影响。

在 GitHub 网站上打开存储库，或在已启动的 codespace 中查看以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将这些说明限定用于 `**/*.test.ts` 文件。

下一节将 `Code lacks documentation` 问题分配给 cloud agent 时，留意它创建的拉取请求中的 TSDoc 文档注释、TypeScript 约定和文件注释头。这些内容来自上述说明文件。后续练习审查 PR 时还会再次提到。

## 为 Copilot cloud agent 设置开发环境

无论由谁编写代码，通常都需要特定环境并运行一些设置脚本，确保环境就绪。将任务分配给 Copilot 时也是如此，因为它的工作方式类似软件工程师（SWE）。

Cloud agent 使用 [GitHub Actions][github-actions] 环境执行工作。你可以通过在 `.github/workflows/copilot-setup-steps.yml` 中创建[专用设置工作流][setup-workflow]，在它开始工作前自定义环境，为其提供所需的开发工具和依赖项。为简化实验流程并提供学习机会，项目已预先配置此工作流。它会为 Copilot 安装 Node.js、项目依赖项和端到端测试所需的浏览器二进制文件，并迁移和填充单体 Astro 应用使用的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你在存储库的“Actions”选项卡中测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 仅设置步骤实际需要的最低权限。Copilot 会获得自己的令牌来执行操作。
    permissions:
      # 如果设置步骤需要克隆存储库（例如安装依赖项），则需要 `contents: read` 权限。如果设置步骤不克隆存储库，Copilot 会在步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现在基于 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，为构建和测试准备数据。
      - name: Set up the database
        run: npm run db:setup
```

它看起来与其他 GitHub 工作流相同，但有几个关键点：

- 它只包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会运行此作业。
- 留意 `workflow_dispatch` 触发条件，它允许你在存储库的 **Actions** 选项卡中手动运行工作流。这有助于测试工作流是否成功，而不必等待 Copilot 启动它。

## 添加文档

虽然人人都了解文档的重要性，但大多数项目的信息都已过时或根本缺少文档。这类技术债务常常得不到处理，降低工作效率，也增加维护代码库或引导新成员的难度。Copilot 擅长创建文档，因此很适合将此问题交给 Copilot cloud agent。它会在后台生成所需文档，后续练习中再回来审查。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新问题对话框。
4. 选择 **Blank issue** 创建空白问题。
5. 将 **Title** 设为 `Code lacks documentation`。
6. 将 **Description** 设为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建问题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将 Copilot 分配给问题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开刚创建的拉取请求（PR），其标题类似 `[WIP]: Code lacks documentation`。如果列表中尚未显示新 PR，请稍等片刻并刷新浏览器窗口。
12. 几分钟后，Copilot 应会创建待办列表。

> [!NOTE]
> Copilot 在 PR 中生成待办列表可能需要几分钟。它需要先创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的最佳方案。

13. 查看待办列表和它计划执行的任务。
14. 向下滚动拉取请求时间线，查看 Copilot 已开始处理问题的更新。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态指示器。

16. 浏览实时会话，了解 Copilot 如何解决问题，包括探索代码并了解当前状态、暂停思考并确定合适计划，以及编写代码。

这可能需要几分钟。Copilot cloud agent 的主要目标之一是异步执行任务，让我们能专注于其他工作。接下来将利用这一功能：先为 Copilot cloud agent 分配另一项任务，然后继续编写应用代码、添加功能。

## 为游戏详情页添加相关游戏部分

如前所述，GitHub Copilot cloud agent 的一大优势是可以拆分工作：你专注于一组任务，它则处理另一组。即使为设计团队添加相关游戏部分不一定需要很长时间，也可以将时间留给其他工作。现在把这项任务分配给 Copilot cloud agent！

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新问题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设为 `Show related games on the game details page`。
6. 将 **Description** 设为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. 选择 **Create** 创建问题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将 Copilot 分配给问题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

稍后，你会在问题的第一条评论中看到一组 👀，表示 Copilot 已开始处理！

![Copilot 使用眼睛表情符号表示正在处理问题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在认真处理第二个请求！Copilot cloud agent 的工作方式类似软件工程师，因此无需一直监视它；等任务完成后再审查即可。现在来创建并使用自定义代理。

## 总结和后续步骤

本课介绍了 [GitHub Copilot cloud agent][copilot-agents]。可以将问题分配给 cloud agent，让它异步工作。你可以让 Copilot 处理技术债务、开发新功能，或协助将代码迁移到其他框架。

本课介绍了以下内容：

- 如何自定义代码生成环境。
- 如何确保操作安全。
- 明确限定问题范围的重要性。
- 如何将问题分配给 Copilot。

Copilot cloud agent 正在后台工作，现在可以开始创建和使用自定义代理。[Copilot cloud agent 还支持 MCP 服务器][cloud-agent-mcp]，并可使用前面模块介绍的自定义说明。

## 资源

- [关于 Copilot cloud agent][copilot-agents]
- [将 GitHub 问题分配给 Copilot][assign-issue]
- [Copilot cloud agent 设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义代理 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
