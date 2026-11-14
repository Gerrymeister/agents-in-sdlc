---
title: "演習 4 - エージェントを監視して管理する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

直前の演習では、ユーザーエクスペリエンスの改善と機能追加を目的とする 3 つのタスクを Copilot クラウドエージェントに依頼しました。クラウドエージェントは非同期かつ自律的に作業しますが、タスクを監視することも重要です。

クラウドエージェントに割り当てたタスクを管理する方法はいくつかあります。[GitHub.com のエージェントページ][agents-page]もその 1 つです。この mission control では、オープンなプルリクエスト (PR) があるすべてのエージェントタスクを確認できます。実行内容を調べたり、進行中のセッションに指示を送って作業を調整したりできます。

このレッスンでは、次のことを行います。

- エージェントページでクラウドエージェントのタスクを監視します。
- 進行中のセッションに指示して、機能を追加するよう依頼します。

## シナリオ

高コントラストモードの作成をエージェントに割り当てた後、チームはライトモードも追加することにしました。サイトのスタイル更新と切り替え機能の実装をすでに行っているため、同じ作業に含めるのが合理的です。エージェントにライトモードと高コントラストモードの両方を追加させましょう。

## Copilot クラウドエージェントのタスクを確認する

Copilot クラウドエージェントに割り当てたタスクの現在の状態を確認しましょう。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メインペインと左ペインの両方に表示されるタスク一覧を確認します。次のような、Copilot に割り当てたタスクが表示されます。
   - コードのドキュメントを更新する。
   - ゲームを変更する API を生成する。
   - Web サイトに高コントラストモードを追加する。
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。次のような操作が含まれます。
   - リポジトリからコードをチェックアウトする。
   - Copilot が作業する環境を作成する。
   - MCP サーバーを設定する。
   - 割り当てられたタスクを完了するための各種手順を実行する。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や実施方法によって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認します。PR と変更されたファイルを確認して、さらに進捗を監視できます。

## クラウドエージェントに指示する

実行中のタスクを確認できたので、高コントラストモードの作業中にライトモードの切り替えも追加するよう Copilot に依頼します。

1. 高コントラストモードの追加に関連するセッションを選択します。Copilot が付けた名前や作業状態によって、タイトルは異なります。

   ![Mission control のアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セッションを数分間確認し、セットアップが完了して作業が始まったことを確かめます。次のようなメッセージが表示されたら作業開始の目安です。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![Copilot の作業中にセッションを指示するダイアログが強調表示されたエージェントページのスクリーンショット](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に反映することを確認します。

## Copilot に作業を任せる

これまでと同様に、Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で作業していたステップが終わると、新しい依頼が作業の流れに追加されます。

前と同じように完了には数分かかります。これまでに学んだことを振り返りましょう。

## まとめと次のステップ

Copilot エージェントページについて学びました。ここは GitHub Copilot クラウドエージェントのタスクを監視し、指示を出すための中心的な場所です。すべてのアクティブなタスクと完了済みタスクを追跡し、作業をレビューし、進行中のタスクに追加の指示を送って範囲を調整できます。

この演習では、次の概念を確認しました。

- クラウドエージェントのタスクを監視するため、mission control とエージェントページを使いました。
- 進行中のセッションに追加の機能を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、Copilot が作成したプルリクエストを[反復的に改善する][next-lesson]方法を確認しましょう。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復的に改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
