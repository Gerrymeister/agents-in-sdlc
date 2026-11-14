---
title: "演習 1 - カスタム指示 (クラウドエージェント)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot クラウドエージェント →][next-lesson]

生成 AI を使ううえで、コンテキストは重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が知っておくべき背景情報がある場合は、そのコンテキストを参照できるようにしましょう。[指示ファイル][instruction-files]を使うと、何を実行するかだけでなく、どのように実行してほしいかも Copilot に伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスを指定した指示ファイルを使い、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加します。

> [!NOTE]
> VS Code や CLI の演習とは異なり、ここではプロンプトを変更前後に実行して比較しません。Copilot クラウドエージェントは GitHub イシューに対して非同期で作業するため、影響をリアルタイムで並べて示すのが難しいためです。この演習の後半で、クラウドエージェントが作成するプルリクエストをレビューし、指示ファイルの影響を確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインと要件があります。たとえば、次のとおりです。

- データレイヤーには必ずユニットテストを用意します。
- UI はダークモードにし、現代的な印象にします。
- コードのドキュメントには TSDoc のドキュメントコメントを使用します。
- 各ファイルの先頭に、ファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使って、これらのプラクティスに沿ってタスクを実行するための情報を Copilot に提供しましょう。

### カスタム指示

カスタム指示を使うと、コンテキストや希望を Copilot に伝え、コーディングスタイルや要件を理解させることができます。より適切な提案やコードスニペットを得るのに役立ちます。リポジトリ全体に適用する指示も、特定の種類のファイルに適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**リクエストで Copilot に送信される単一の指示ファイルです。Copilot へのほとんどのチャットや CLI リクエストに関係する、プロジェクト全体の情報を記載します。技術スタック、開発中のものの概要、ベストプラクティス、全体的なガイダンスなどが該当します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイルの種類に指針を与えるファイルです。TypeScript や Astro などの言語、UI コンポーネントの作成、ユニットテストの作成といったタスクのガイドラインを記載できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使用され、コード補完や次の編集候補には使用されません。
>
> Copilot Chat、Copilot CLI、Copilot クラウドエージェントは、リポジトリレベルの指示と、`applyTo` フロントマターがある `*.instructions.md` ファイルの両方をコード生成に使用します。
>
> Copilot は、AGENTS.md や CLAUDE.md など、ほかの標準形式の指示ファイルにも対応しています。詳細は[カスタム指示のサポート][custom-instructions-support]を参照してください。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成について詳しく説明することは、このワークショップの範囲外です。ただし、サンプルプロジェクトの例から、代表的な方法を確認できます。基本的には、次のようにします。

- `copilot-instructions.md` には、開発するものの説明、プロジェクト構成、全体的なコーディング標準など、プロジェクトレベルの指針を記載します。
- `*.instructions.md` ファイルには、ファイルの種類 (ユニットテスト、Astro コンポーネント、データレイヤーなど) や特定のタスクに関する指示を記載します。
- 自然な言葉を使い、明確なガイダンスを提供します。コードの良い例と悪い例を示します。

指示ファイルの作成に唯一の正解がないのと同じように、AI の使い方にも決まった方法はありません。プロジェクトに最適な方法は、試行を通じて見つけましょう。

> [!TIP]
> GitHub Copilot を使用するすべてのプロジェクトで、十分な指示ファイルを用意してください。このプロジェクトのファイルを確認すると、[UI 更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けの指示があることが分かります。
>
> Copilot に指示ファイルを生成してもらうこともできます。画面ごとに操作方法が異なります (たとえば、VS Code の **Configure Chat → Generate Agent Instructions**、Copilot CLI の `/init`)。この演習では、関連する操作方法を説明します。
>
> テンプレートや出発点を探している場合は、指示ファイル、カスタムエージェント、その他のリソースをまとめたリポジトリ [awesome-copilot][awesome-copilot]を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに用意されている指示ファイルを確認しましょう。共通の `copilot-instructions.md` と、タスク別の `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下にある **GitHub Actions Workflows** の指針も確認してください。これらは Copilot とのすべてのやりとりに適用されます。
3. `.github/instructions` フォルダーを開いて確認します。Astro ファイル、Drizzle データレイヤー、テストなどの指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、適用対象のファイルをリポジトリルートからの glob で指定していることを確認します。たとえば `**/*.test.ts` に一致する TypeScript のテストファイルに適用されます。
5. このプロジェクトでのユニットテスト作成に関する指示を確認します。
6. 最後に `.github/instructions/drizzle.instructions.md` を開いて末尾までスクロールし、他の指示ファイル (例: `unit-tests.instructions.md`) や既存ファイルへのリンクを確認します。これにより、大きな指示を再利用できる小さなファイルに分割し、コード生成の例を Copilot に示せます (ここに記載されたパスはリポジトリルートではなく、指示ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションではプロジェクトのコーディング標準を説明していますが、コード内のドキュメントはまだ必須ではありません。次の手順で、TSDoc のドキュメントコメントとファイルコメントのヘッダーに関する規則を追加します。

## リポジトリの新しい標準を追加する

ここで行う編集は 1 つです。コードに TSDoc のドキュメントコメントとファイルレベルのコメントヘッダーを付けるという、プロジェクト全体の規則を追加します。クラウドエージェントは、この演習の後半で割り当てるイシューに取り組むときに、この規則を読み込みます。

編集の前に作業用のブランチを準備します (クラウドエージェントはイシューの対象ブランチにある指示を読み取りますが、変更を整理してコミットします)。

1. Codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

前述のとおり、`.github/copilot-instructions.md` は Copilot にプロジェクト全体の情報を提供します。コード提案の改善に役立つよう、リポジトリのコーディング標準を記載しましょう。

1. `.github/copilot-instructions.md` をもう一度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。プロジェクトのコーディング標準は記載されていますが、コード内ドキュメントの規則がまだありません。そのため、生成されたヘルパーにはドキュメントコメントがありませんでした。
3. 既存の標準の直後に、ファイルコメントのヘッダーと TSDoc のドキュメントコメントを追加する次の Markdown 行を挿入します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、指示ファイルはリポジトリレベル (`.github/copilot-instructions.md`) に作成して全体に適用することも、特定の言語、ファイルの種類、タスク向けに `*.instructions.md` として作成することもできます。今回追加したドキュメントコメント規則など、プロジェクト全体の標準にはリポジトリレベルのファイルが適しています。

## 指示の更新をコミット、プッシュ、マージする

クラウドエージェントは、イシューが対象とするブランチから指示ファイルを読み込みます。次の演習では `main` からブランチを作成してイシューを割り当てるため、クラウドエージェントが指示を読み取れるよう、変更を `main` にマージする必要があります。

1. 変更をステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを開き、マージします。次の演習でクラウドエージェントに割り当てるイシューでは、更新された指示が読み込まれます。

> [!TIP]
> このワークショップで `main` に直接作業したい場合は、ブランチを作成せず `main` にコミットしてもかまいません。実際のプロジェクトと同じように進めるため、ここではブランチを使っています。

## まとめと次のステップ

Copilot がこのプロジェクトの指示ファイルからコンテキストを読み込む方法を確認し、`.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加しました。この演習の後半で、クラウドエージェントが作成するプルリクエストにこの標準が反映されていることを確認します。

次は[最初のイシューを Copilot クラウドエージェントに割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot に適したカスタム指示を書くための 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
