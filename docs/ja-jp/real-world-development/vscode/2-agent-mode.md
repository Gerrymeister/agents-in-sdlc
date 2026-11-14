---
title: "演習 2 - Copilot Agent Mode で機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションの単純な更新でも、複数のファイルを変更し、テストを実行するなどの作業が必要になることがあります。開発者は通常、必要なファイルを探し、変更を加え、テストを実行し、問題をデバッグし、見落としたファイルを探して、さらに更新するという流れで作業します。こうした作業は際限なく続くことがあります。

このような場面で役立つのが Copilot Agent Mode です。

Copilot Agent Mode は、IDE 内でより自律的に作業するように設計されています。開発者と同様に、既存のプロジェクト構成を調査し、必要な更新を行い、テストなどのタスクを実行し、見つかった問題を自動的に修正します。Agent Mode を使ってサイトに新しい機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、エージェントモードとクラウドエージェントは異なる体験を提供します。エージェントモードは IDE 内で動作し、すばやいフィードバックと対話が可能です。クラウドエージェントはチームメンバーのように非同期で作業し、イシューやプルリクエストを通じてやりとりします。

この演習では、次のことを学びます。

- Copilot Agent Mode がプロジェクトを調査し、関連ファイルを特定して、複数の変更を調整する方法。
- GitHub Copilot Agent Mode で UI とデータレイヤーにまたがる新機能を実装する方法。
- Copilot Agent Mode が生成した変更とテストを、コードベースにマージする前にレビューする方法。

## シナリオ

ゲームの一覧が増えてきたため、利用者がカテゴリとパブリッシャーで絞り込めるようにしたいと考えています。前の演習ではパブリッシャーヘルパーを追加しました。今度は Copilot Agent Mode を使って、残りのデータレイヤー、UI、テストの作業を完了します。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、Tailspin Toys の現在の機能を確認しましょう。

この Web サイトは、ボードゲーム向けのクラウドファンディングプラットフォームで、開発者向けのテーマを使っています。ゲームの一覧と詳細を表示します。単一の Astro アプリで、ビルド時に静的 HTML としてページをレンダリングします。ページは Drizzle ORM を通じてフロントマターからローカルの SQLite データベースを直接クエリします。別のバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` にあり、インタラクティブな機能は標準 DOM API を使った小規模な Astro `<script>` で追加します。

### Web サイトを起動する

`npm run dev` タスクを使うと、単一の Astro 開発サーバーを起動できます。GitHub Codespace で次の手順を実行します。

1. Codespace に戻ります。前の演習を終えて `main` に戻っているはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルウィンドウを開きます。
3. フィルタリング作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーが起動すると、次のような URL が表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルの Astro サーバーアドレス `http://localhost:4321` を、<kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら選択して Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的に転送されます。これは Codespace の Web サーバーをホストするプライベートトンネルです。

### Web サイトを確認する

Web サイトが起動したら、機能を確認できます。主な機能は次のとおりです。

- **ホームページ**: タイトル、画像、説明とともにボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、パブリッシャー、カテゴリなどの情報を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、Codespace に戻り、Copilot Chat パネルを開き、**New Chat** を選択して新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は、各手順で説明します。

Web サイトの初期実装は機能しますが、新しい機能を追加したいと考えています。まずバックログを確認しましょう。テンプレートからプロジェクトをセットアップすると、イシューのバックログが自動的に作成されます。GitHub Copilot にそれらの項目を表示するよう依頼します。

1. Chat ビューのエージェントドロップダウンから **Agent** を選択します。**Agent** はファイルを横断して変更を計画および実装し、ターミナルコマンドを実行してツールを呼び出します。

   ![Chat ビューでエージェントピッカーを表示したスクリーンショット](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が、特定のモデルを推奨しているわけではありません。ワークショップの作成時に Claude Sonnet 4.5 を使用したため、手順にも記載しています。提案されるコードに一貫性を持たせ、取り組みやすくすることを意図しています。ただし LLM は確率的に動作するため、手順に記載したものとは異なる提案が表示されることがあります。これは正常な動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使うことがありますが、タスクは同様に完了できます。

3. 次のプロンプトを Copilot に送信して、イシューのバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべてのイシューを一覧表示するコマンドを実行します。
5. 生成されたイシューの一覧を確認します。

Copilot が、利用者にとって役立つものを基準にイシューを優先順位付けしたことを確認してください。

## 指示ファイルを確認する

エージェントにコードを生成させる前に、Copilot のコンテキストとなる指示ファイルを確認しましょう。Web サイトに機能を追加する方法を説明する [ユーザーインターフェイス (UI) の指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加に関する全体的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト容易性、アクセシビリティの原則。
   - Astro、Tailwind CSS、Drizzle など、ファイルの種類別の指示へのリンク。

> [!TIP]
> 指示ファイルから、他の指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルの場所からの相対パスです。指示を再利用し、大きな内容を扱いやすい小さな単位に分け、コード生成の例を Copilot に示せます。

## フィルタリング機能を実装する

フィルタリングを完成するには、少なくとも次の 3 つの個別の更新が必要です。

- データレイヤー (`src/lib/`) の残りのフィルタリングロジックを追加または調整します。
- フィルタリング動作のテストを追加または更新します。
- ゲーム一覧ページにフィルタリング UI を追加します。

コードベースにマージする前に、テストを実行してすべて成功することも必要です。Copilot Agent Mode にこれらのタスクを実行させましょう。

1. Copilot との現在の会話を続けるか、**New Chat** を選択して新しく始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して指示ファイルを追加します。

   ![UI 指示ファイルを選択する例のスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェントドロップダウンで、引き続き **Agent** が選択されていることを確認します。

   ![Chat ビューでエージェントピッカーを表示したスクリーンショット](../../../_images/shared-chat-mode-selector.png)

4. モデルとして **Claude Sonnet 4.5** が選択されていることを確認します。
5. 次のプロンプトを使い、バックログにある関連イシューの要件に基づいて機能を実装するよう Copilot に依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調査し、機能に関係するデータレイヤーヘルパー、UI、テストを探してから、ファイルの編集とテストを行う様子を確認します。

   ![Copilot がプロジェクトファイルを調査しているスクリーンショット](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行などを行います。タスクやコードベースの複雑さによって数分かかることがあります。作業中、コードエディターに **Keep** と **Undo** が表示されることがあります。作業が終わると、すべての変更に **Keep** または **Undo** が表示されるため、進行中に選択する必要はありません。

7. Copilot に求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat のペインでユーザーにテスト実行の確認を求めるダイアログのスクリーンショット](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりすることがあります。問題ありません。Copilot はタスクが完了し、エラーがないと判断するまでコード生成とテストを繰り返します。

   ![Copilot Agent Mode との会話が完了した状態のスクリーンショット](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを開いているブラウザーに戻り、新しい機能を試します。
11. 動作を確認し、コードのレビューが終わったら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

GitHub Copilot Agent Mode を使って Tailspin Toys Web サイトに新機能を追加する方法を確認しました。この演習では、次のことを学びました。

- GitHub Copilot Agent Mode で UI とデータレイヤーにまたがる新機能を実装する方法。
- Copilot Agent Mode がプロジェクトを調査し、関連ファイルを特定して、複数の変更を調整する方法。
- Copilot Agent Mode が生成した変更とテストを、コードベースにマージする前にレビューする方法。

次は、[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成しましょう。

### ボーナス演習 - ページングを実装する

ゲームの一覧が増えるとページングが必要になります。この演習で学んだことを使い、Copilot にページングを実装するよう依頼してください。次の点を検討します。

- 既存の指示ファイルを使うなど、現在のベストプラクティスに従います。
- ページサイズを利用者が選択できるようにするか、固定にするかなど、ページングの方式を検討します。
- 希望する実装に必要なガイダンスをプロンプトで Copilot に伝えます。
- GitHub Copilot に変更やコンテキストを追加で伝えながら、何度かやりとりする必要がある場合があります。これは Copilot を使う通常の流れです。

## リソース

- [Copilot の ask、edit、agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
