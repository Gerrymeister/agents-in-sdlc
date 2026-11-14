---
slug: ja-jp/real-world-development/cloud
title: "GitHub Copilot クラウドエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

**[GitHub Copilot クラウドエージェント](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent)**を使うと、GitHub Copilot がクラウドで非同期に作業できます。GitHub 上で作業を割り当てると、クラウドエージェントはバックグラウンドでリポジトリを調査し、変更を行い、プルリクエストを作成します。その間、別の作業を続けられます。

各演習では、まずクラウドエージェントが従うカスタム指示を追加し、GitHub イシューを割り当てて実装させます。カスタムエージェントを確認して使い、作業方法を調整し、エージェントダッシュボードからセッションを監視して指示し、最後にプルリクエストをレビューして結果を改善します。

## 演習

| 演習 | トピック | 説明 |
|----------|-------|-------------|
| [0. 前提条件][ex0] | セットアップ | リポジトリと Codespace を作成する |
| [1. カスタム指示][ex1] | コンテキスト | クラウドエージェントが従うカスタム指示を追加する |
| [2. クラウドエージェント][ex2] | 非同期エージェント | Copilot クラウドエージェントにイシューを割り当てる |
| [3. カスタムエージェント][ex3] | 専門エージェント | カスタムエージェントを確認して使う |
| [4. エージェントの管理][ex4] | 監視 | エージェントのセッションを監視して指示する |
| [5. 反復][ex5] | レビュー | PR をレビューし、Copilot の作業を改善して、次の手順を選ぶ |

## 前提条件

このワークショップに参加する前に、次の条件を満たしていることを確認してください。

- [ ] 有効な **Copilot Student、Pro、Pro+、Business、Enterprise** プランを利用できる GitHub アカウント
- [ ] アカウントまたは組織で Copilot クラウドエージェントが有効になっている

> [!TIP]
> 有料プランを利用していない場合でも、認証済みの学生は[GitHub Education][callout-student-plan-education]を通じて GitHub Copilot を無料で利用できます。**Copilot Student** プランには、このワークショップで使うエージェント、MCP、コードレビュー、Copilot CLI の機能が含まれているため、すべての演習を完了できます。

[callout-student-plan-education]: https://github.com/education/students

> [!NOTE]
> クラウドエージェントは **Copilot Student、Pro、Pro+、Business、Enterprise** で利用できます。Copilot Free を除くすべてのプランが対象です。Copilot Business または Enterprise の場合、組織の管理者が有効にする必要があります。

> [!NOTE]
> この演習では MCP を扱いません。Copilot で MCP サーバーを使う方法については、[CLI の演習][cli-workshop]または [VS Code の演習][vscode-workshop]を参照してください。

## 始める

**[演習 0: 前提条件から始める →][ex0]**

[ex0]: 0-prerequisites/
[ex1]: 1-custom-instructions/
[ex2]: 2-cloud-agent/
[ex3]: 3-custom-agents/
[ex4]: 4-managing-agents/
[ex5]: 5-iterating/
[cli-workshop]: ../cli/
[vscode-workshop]: ../vscode/
