---
title: "演習 5: GitHub Copilot の作業を反復的に改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、ユーザーエクスペリエンスの改善と機能追加を目的に、GitHub Copilot と複数のタスクに取り組みました。コードへのドキュメント追加、デザインチームが試せる関連ゲーム機能の作成、高コントラストとライトモードの切り替えを含むアクセシビリティ機能の実装を依頼しました。コードの変更を確認し、必要であれば Copilot にフィードバックを送って改善しましょう。

### シナリオ

これまでに何度も説明したように、生成 AI を導入してもソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスに沿って作業します。チームのレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能に関して GitHub Copilot が提案した内容を確認しましょう。

## セキュリティと GitHub Copilot クラウドエージェント

Copilot クラウドエージェントは非同期で、監視を受けずに作業するため、安全性を保つためのセキュリティ制約が設けられています。たとえば、次のとおりです。

- Copilot はリポジトリへの読み取りアクセスと、コード作業用のブランチへの書き込みアクセスのみを持ちます。
- クラウドエージェントは GitHub Actions 内で実行され、作業用の分離された一時環境を作成します。
- GitHub Actions ワークフローを実行するには、人間の承認が必要です。
- MCP サーバーを含む[外部リソースへのアクセスは既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

最初に GitHub Copilot クラウドエージェントが作成した、コードへのドキュメント追加プルリクエスト (PR) を確認します。GitHub.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR を開くと、GitHub Copilot がファイアウォールでブロックされているという警告が表示される場合があります。Copilot は外部 MCP サーバーへの呼び出しなど、既定で外部リソースへのアクセスが制限されているため、これは**想定された動作**です。必要であれば、[Copilot クラウドエージェントのファイアウォールを構成または無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択してプルリクエストの一覧を開きます。
3. **Add missing documentation** などのタイトルのプルリクエストを開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストのタイトルに **[WIP]** が含まれます。その場合は、Copilot が作業を完了するまで待ちます。数分かかることがあります。休憩を取ったり、学んだことを振り返ったりしてください。

4. プルリクエストの準備ができたら **Files changed** タブを選択し、変更をレビューします。

   ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. TSDoc のドキュメントコメントやその他のドキュメントを含む、更新されたコードを確認します。変更内容は状況によって異なります。

   変更を確認しながら、TSDoc のドキュメントコメント、TypeScript 規約、コメントヘッダーを探します。これらは演習 2 の最初に確認したカスタム指示ファイルから反映されています。

6. 更新をレビューし、問題がなければ **Conversation** タブに戻って下にスクロールします。
7. ワークフローの承認待ちを示す表示を探します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. プルリクエストのチェックセクションにワークフローがキューに登録されます。問題がなければ、単一の Astro アプリのプロジェクトチェックが成功します。完了まで数分かかることがあります。

## GitHub Copilot に変更を依頼する

Copilot とのプルリクエスト作業は一方通行ではありません。チームの他のメンバーと同様に、Copilot をプルリクエストのコメントやコードのインラインコメントでメンションできます。Copilot はコメントを確認し、対応するための新しいセッションを開始します。結果は確定的でないため、必ずこう依頼するという文面はありません。たとえば、次のような変更を依頼できます。

- 各コードファイルの先頭に、役割を簡潔に説明するコメントヘッダーを追加する。
- TypeScript と Astro ファイルに TSDoc のドキュメントコメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. 上記の例などを使い、生成されたドキュメントへの変更を依頼するコメントを追加し、通常のユーザーと同じように **@copilot** をメンションします。
2. **View Session** を選択して、Copilot が作業する様子を確認します。更新のために新しいセッションが始まります。
3. **Back to pull request** を選択すると、プルリクエストに戻れます。

   ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了すると、プルリクエストに新しいコミットが表示されます。
5. **Files changed** タブを選択して変更をレビューします。

満足するまで改善を繰り返してください。準備ができたら、ドラフト PR をレビュー可能な状態にして、`main` ブランチにマージできます。

![PR をレビュー可能な状態にする](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページに関連ゲームを表示するイシューに対して Copilot が作成した PR を確認しましょう。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 更新をレビューし、問題がなければ **Conversation** タブに戻って下にスクロールします。
6. ワークフローの承認待ちを示す表示を探します。
7. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

8. プルリクエストのチェックセクションにワークフローがキューに登録されます。問題がなければ、単一の Astro アプリのプロジェクトチェックが成功します。完了まで数分かかることがあります。
9. **省略可能:** Codespace でこのブランチに切り替えて、関連ゲーム機能を手動でテストすることもできます。Codespace に移動し、ターミナルを開いて、次のコマンドを実行します (`<branch-name>` は Copilot が作成したブランチ名、例: **copilot/fix-8** に置き換えます)。

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot が関連ゲーム機能を実装しました。これまでと同様に、Copilot クラウドエージェントに変更を依頼して反復的に改善できます。たとえば、表示する関連ゲームの数を変更したり、コメントヘッダーと TSDoc のドキュメントコメントを追加したりできます (このイシューは、カスタム指示を更新する**前**に割り当てたことを思い出してください)。前と同じように、**Conversation** タブにコメントを追加すれば、Copilot が確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティカスタムエージェントを使って実装した機能をレビューします。この PR には、演習 3 で依頼した高コントラストモードと、演習 4 の mission control で追加を依頼したライトモードの両方が含まれています。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストのタイトルに **[WIP]** が含まれます。その場合は、Copilot が完了するまで待ちます。数分かかることがあります。

4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 実装を確認し、特に次の点を確認します。
   - モード切り替え用の UI コンポーネント。
   - 利用者の設定を保存する local storage の使い方。
   - 高コントラストモードとライトモードの CSS またはスタイルの変更。
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)。
   - モード切り替えを管理する JavaScript/TypeScript コード。

6. 更新をレビューし、問題がなければ **Conversation** タブに戻って下にスクロールします。
7. ワークフローの承認待ちを示す表示を探します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. プルリクエストのチェックセクションにワークフローがキューに登録されます。問題がなければ、単一の Astro アプリのプロジェクトチェックが成功します。完了まで数分かかることがあります。
10. **省略可能:** Codespace でこのブランチに切り替えて、アクセシビリティ機能を手動でテストできます。Codespace に移動し、ターミナルを開いて、次のコマンドを実行します (`<branch-name>` は Copilot が作成したブランチ名に置き換えます)。

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

   その後、アプリケーションを起動し、ブラウザーで高コントラストとライトモードの切り替えを試します。期待どおりに動作し、ページを再読み込みしても設定が維持されることを確認します。

アクセシビリティカスタムエージェントが、Copilot にアクセシビリティのベストプラクティスに沿った機能を実装するよう導いたことを確認してください。アクセシビリティ上の問題や改善点があれば、前の PR と同様に、コメントで **@copilot** をメンションして変更を依頼できます。

## 省略可能な演習 — さらに作業を委任する

クラウドエージェントは、実際のバックログ項目を渡して別の作業に集中できるときに効果を発揮します。習慣にするため、リポジトリにいくつかイシューを登録し、Copilot に割り当てましょう。次のようなタスクがあります。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセスヘルパーに入力検証とエラー処理を追加する。

## まとめ

クラウドエージェントの演習を完了しました。各レッスンを通じて、次のことを行いました。

- **このリポジトリに用意されたカスタム指示ファイルを確認し**、後でクラウドエージェントの出力への影響を確認できるようにしました。
- **イシューを Copilot クラウドエージェントに割り当て**、環境の準備、計画、非同期の実行を確認しました。
- アクセシビリティ用のカスタムエージェントを作成して使い、高コントラストとライトモードの切り替えを追加しました。
- Copilot エージェントページを mission control として使い、進行中のアクセシビリティセッションを監視して指示しました。
- クラウドエージェントのプルリクエストをレビューして反復的に改善し、**@copilot** をメンションして変更を依頼し、ワークフローを承認しました。

## レビューと次のステップ

クラウドエージェントの演習を完了しました。さらに学びたい場合は、他の演習で今回の内容を補完できます。

- 🖥️ **[VS Code の演習](../../vscode/)** — IDE から Copilot Chat のエージェントモードと MCP 統合を試します。
- 💻 **[CLI の演習](../../cli/)** — Plan と Autopilot モード、エージェントスキル、QA カスタムエージェント、Playwright MCP、Agent Merge、省略可能な `/delegate` によるクラウドエージェント連携を使って、ターミナルからレビュー済みの変更を 2 つ完成させます。

自分のリポジトリでは、次のアイデアを試してください。

- リファクタリングやテスト範囲の拡充に関するイシューをクラウドエージェントに割り当てる。
- 別の分野向けのカスタムエージェントを作成する。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub イシューを Copilot に割り当てる][assign-issue]
- [Copilot クラウドエージェントのセットアップワークフローのベストプラクティス][cloud-agent-best-practices]
- [Copilot クラウドエージェントのファイアウォールを構成する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
