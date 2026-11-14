---
title: "演習 2 - GitHub Copilot クラウドエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩んでいない組織は、ほとんどないでしょう。未解決のセキュリティ課題、更新が必要なレガシーコード、実装の時間がなくバックログに残った機能リクエストなどがその例です。GitHub Copilot のクラウドエージェントは、コードの更新や機能の追加などのタスクを自律的に実行します。作業が完了すると、人間の開発者がレビューできるドラフト PR を作成します。面倒な作業を任せて開発を加速し、開発者はより大きな課題に集中できます。

Copilot クラウドエージェントについて、次のことを学びます。

- コード生成環境をカスタマイズする方法。
- 操作を安全に行う方法。
- 範囲が明確なイシューが重要な理由。
- イシューを Copilot に割り当てる方法。

## シナリオ

Tailspin Toys には解消したい技術的負債があります。サイトの最初のバージョンを開発した委託先は、ドキュメントを適切に整備しませんでした。実際、ほとんど何もありません。まず、アプリケーションのすべてのエクスポート関数に TSDoc のドキュメントコメントを追加したいと考えています。

また、デザインチームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属する他のタイトルを「関連ゲーム」として表示し、利用者が閲覧を続けられるようにします。まだ完成度の高い実装は必要なく、UX の受け入れテストに使えるものを求めています。この作業は現在ブロッカーですが、優先度の高い他のイシューが先にあります。

どちらも、優先順位が下がりやすいタスクの例であり、Copilot クラウドエージェントに割り当てるのに適しています。Copilot クラウドエージェントに非同期で作業させれば、開発者は別のタスクに集中し、後から結果をレビューして要件どおりか確認できます。

## GitHub Copilot クラウドエージェントについて

[GitHub Copilot クラウドエージェント](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)は、人間の開発者と同様にバックグラウンドでタスクを実行できます。[GitHub イシューを Copilot に割り当てる][assign-issue]など、複数の方法で作業を依頼できます。割り当てると、Copilot は進捗を記録するドラフトプルリクエストを作成し、環境を準備して作業を始めます。セッションの進行中でも完了後でも、その内容を確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単そうな作業にも、掘り下げると複雑な部分があります。[Copilot クラウドエージェントのタスク範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペアプログラマーとして扱い、段階的に進め、学びながら試して調整してください。生成 AI を加えても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習ではカスタム指示と、その働きを確認しました。カスタム指示の演習をすでに行っている場合は、`.github/copilot-instructions.md` の効果を確認したはずです。まだの場合は、ここで簡単に見ておきましょう。Copilot クラウドエージェントに作業を割り当てる前に、このリポジトリに用意された指示ファイルを読み取り専用で確認し、後でその影響を探せるようにします。

リポジトリの GitHub Web UI、または起動済みの Codespace で次のファイルを開きます。

- `.github/copilot-instructions.md` - **Code standards** セクション、特に TypeScript 規約と TSDoc コメントの要件を確認します。
- `.github/instructions/unit-tests.instructions.md` - `applyTo` フロントマターで、この指示が `**/*.test.ts` ファイルに適用されることを確認します。

次のセクションで `Code lacks documentation` イシューをクラウドエージェントに割り当てる際、生成されたプルリクエストに TSDoc コメント、TypeScript 規約、コメントヘッダーが反映されているか確認してください。後の演習で PR をレビューするときに、改めてこの点を取り上げます。

## Copilot クラウドエージェントの開発環境を設定する

誰がコードを書く場合でも、通常は環境の準備やスクリプトの実行が必要です。タスクを Copilot に割り当てる場合も同様です。Copilot はソフトウェアエンジニアのように作業します。

クラウドエージェントは、作業環境として [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップワークフロー][setup-workflow]を作成して、作業開始前に実行するツールや依存関係をカスタマイズできます。このラボの流れを簡単にし、学習に集中できるよう、事前に設定されています。単一の Astro アプリで、Node.js、プロジェクトの依存関係、エンドツーエンドテスト用ブラウザー、マイグレーションとシード済みの SQLite データベースを Copilot が使えるようにします。

```yaml
name: "Copilot Setup Steps"

# リポジトリの Actions タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 必要な手順に対して、可能な限り最小の権限を設定します。Copilot の操作には独自のトークンが使われます。
    permissions:
      # 依存関係のインストールなど、セットアップ手順の一部としてリポジトリをクローンする場合は `contents: read` 権限が必要です。セットアップ手順でクローンしない場合は、完了後に Copilot が自動でクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL)
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

      # 単一 Astro アプリのビルドとテスト用に、ローカル SQLite データベースをマイグレーションしてシードします。
      - name: Set up the database
        run: npm run db:setup
```

一般的な GitHub ワークフローファイルですが、いくつか重要な点があります。

- `copilot-setup-steps` というジョブが 1 つあります。このジョブは Copilot がプルリクエストの作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーが設定されています。リポジトリの **Actions** タブから手動でワークフローを実行し、Copilot の実行を待たずに正常に動くかテストできます。

## ドキュメントを追加する

ドキュメントが重要なのは誰もが理解していますが、情報が古かったり、まったくなかったりするプロジェクトがほとんどです。このような技術的負債は放置されやすく、生産性を下げ、コードベースの保守や新しい開発者の受け入れを難しくします。Copilot はドキュメントの作成を得意とするため、このタスクは Copilot クラウドエージェントに依頼するのに適しています。バックグラウンドで必要なドキュメントを作成してもらい、後の演習で結果をレビューします。

1. 新しいブラウザータブで github.com のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択してイシューダイアログを開きます。
4. **Blank issue** を選択して新しいイシューを作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. **Create** を選択してイシューを作成します。
8. 右側にある **Assign to Copilot** を選択し、割り当てダイアログを開きます。

   ![Copilot をイシューに割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。一覧に PR が表示されない場合は、少し待ってからブラウザーウィンドウを更新します。
12. 数分後、Copilot が ToDo リストを作成したことを確認します。

> [!NOTE]
> Copilot の ToDo リストが PR に表示されるまで数分かかることがあります。Copilot は環境を作成し (前述のワークフローを実行)、プロジェクトを分析して、問題への最適な取り組み方を検討します。

13. リストと、実行予定のタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot がイシューの作業を開始したという更新を確認します。
15. **View session** ボタンを選択します。

   ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 表示の更新を確認するには、ウィンドウを更新する必要がある場合があります。

16. ライブセッションをスクロールして Copilot の作業を確認します。コードを調査して状態を把握する様子、計画を検討するために一時停止する様子、コードを作成する様子を確認できます。

完了まで数分かかることがあります。Copilot クラウドエージェントの主な目的の 1 つは、非同期で作業して他のタスクに集中できるようにすることです。この機能を活用して、もう 1 つのタスクをクラウドエージェントに割り当て、その間にアプリケーションへ機能を追加するコードを書きましょう。

## ゲーム詳細ページに関連ゲームのセクションを追加する

前述のとおり、GitHub Copilot クラウドエージェントの利点の 1 つは、作業を分けられることです。デザインチーム向けに関連ゲームのセクションを追加する作業に時間がかからないとしても、その時間を別のタスクに使えます。Copilot クラウドエージェントに依頼しましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して新しいイシューダイアログを開きます。
4. **Blank issue** を選択して、空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. **Create** を選択してイシューを作成します。
8. 右側にある **Assign to Copilot** を選択し、割り当てダイアログを開きます。

   ![Copilot をイシューに割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

間もなく、Copilot が作業を開始したことを示す 👀 がイシューの最初のコメントに表示されます。

![Copilot がイシューの作業中であることを示す目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼に取り組み始めました。Copilot クラウドエージェントはソフトウェアエンジニアと同じように作業するため、常時監視する必要はありません。完了後にレビューします。次は、カスタムエージェントの作成と使用を確認しましょう。

## まとめと次のステップ

[GitHub Copilot クラウドエージェント][copilot-agents]について学びました。クラウドエージェントにイシューを割り当て、非同期に作業させることができます。技術的負債の解消、新機能の作成、別のフレームワークへのコード移行などを依頼できます。

この演習では、次の概念を確認しました。

- コード生成環境をカスタマイズする方法。
- 操作を安全に行う方法。
- 範囲が明確なイシューが重要な理由。
- イシューを Copilot に割り当てる方法。

クラウドエージェントがバックグラウンドで作業している間に、カスタムエージェントの作成と利用に取り組みましょう。[Copilot クラウドエージェントは MCP サーバーも利用できます][cloud-agent-mcp]。前のモジュールで学んだカスタム指示も利用できます。

## リソース

- [Copilot クラウドエージェントについて][copilot-agents]
- [GitHub イシューを Copilot に割り当てる][assign-issue]
- [Copilot クラウドエージェントのセットアップワークフローのベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
