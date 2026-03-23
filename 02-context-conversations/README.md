![Chapter 02: Context and Conversations](images/chapter-header.png)

> **AIがコードベース全体を、1ファイルずつではなく一度に把握できるとしたらどうでしょう？**

この章では、GitHub Copilot CLI の真の力である「コンテキスト」を活用する方法を学びます。`@` 構文を使ってファイルやディレクトリを参照し、Copilot CLI がコードベースを深く理解できるようにする方法を習得します。また、セッションをまたいで会話を続ける方法や、数日後でも作業をそのまま再開する方法、そしてクロスファイル分析が単一ファイルレビューでは見逃してしまうバグを発見する仕組みについても学びます。

## 🎯 学習目標

この章を終えると、次のことができるようになります：

- `@` 構文を使ってファイル、ディレクトリ、画像を参照する
- `--resume` と `--continue` で以前のセッションを再開する
- [コンテキストウィンドウ](../GLOSSARY.md#context-window)の仕組みを理解する
- 効果的なマルチターン会話を行う
- 複数プロジェクトのワークフローでディレクトリの権限を管理する

> ⏱️ **想定所要時間**: 約50分（読書20分＋ハンズオン30分）

---

## 🧩 現実世界のたとえ：同僚との作業

<img src="images/colleague-context-analogy.png" alt="Context Makes the Difference - Without vs With Context" width="800"/>

*同僚と同じように、Copilot CLI は心を読む能力を持っていません。情報を多く提供するほど、人間と Copilot の両方がより的確なサポートを提供できます！*

バグを同僚に説明する場面を想像してください：

> **コンテキストなし**: 「book app が動かない。」

> **コンテキストあり**: 「`books.py` の `find_book_by_title` 関数を見てほしい。大文字・小文字を区別しないマッチングができていない。」

Copilot CLI にコンテキストを提供するには、*`@` 構文*を使って特定のファイルを指定します。

---

# 必須：基本的なコンテキスト

<img src="images/essential-basic-context.png" alt="Glowing code blocks connected by light trails representing how context flows through Copilot CLI conversations" width="800"/>

このセクションでは、コンテキストを効果的に活用するために必要なすべての基本を説明します。まずこれらの基本をマスターしましょう。

---

## @ 構文

`@` 記号は、プロンプトの中でファイルやディレクトリを参照するために使います。Copilot CLI に「このファイルを見て」と伝える方法です。

> 💡 **注意**: このコースのすべての例は、このリポジトリに含まれている `samples/` フォルダを使用しているため、すべてのコマンドをそのまま試すことができます。

### 今すぐ試してみましょう（セットアップ不要）

コンピューター上の任意のファイルで試すことができます：

```bash
copilot

# 手元にあるファイルを指定する
> @package.json は何をしますか？
> @README.md を要約してください
> @.gitignore には何が書かれていますか？それはなぜですか？
```

> 💡 **手元にプロジェクトがない場合は？** 簡単なテストファイルを作成してみましょう：
> ```bash
> echo "def greet(name): return 'Hello ' + name" > test.py
> copilot
> > @test.py は何をしますか？
> ```

### 基本的な @ パターン

| パターン | 動作 | 使用例 |
|---------|--------------|-------------|
| `@file.py` | 単一ファイルを参照する | `Review @samples/book-app-project/books.py` |
| `@folder/` | ディレクトリ内のすべてのファイルを参照する | `Review @samples/book-app-project/` |
| `@file1.py @file2.py` | 複数のファイルを参照する | `Compare @samples/book-app-project/book_app.py @samples/book-app-project/books.py` |

### 単一ファイルを参照する

```bash
copilot

> @samples/book-app-project/utils.py が何をするか説明してください
```

---

<details>
<summary>🎬 実際の動作を見てみましょう！</summary>

![File Context Demo](images/file-context-demo.gif)

*デモの出力は異なる場合があります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

### 複数のファイルを参照する

```bash
copilot

> @samples/book-app-project/book_app.py と @samples/book-app-project/books.py の一貫性を比較してください
```

### ディレクトリ全体を参照する

```bash
copilot

> @samples/book-app-project/ のすべてのファイルのエラーハンドリングをレビューしてください
```

---

## クロスファイルインテリジェンス

ここでコンテキストが強力な武器になります。単一ファイルの分析も有用ですが、クロスファイル分析は革新的です。

<img src="images/cross-file-intelligence.png" alt="Cross-File Intelligence - comparing single-file vs cross-file analysis showing how analyzing files together reveals bugs, data flow, and patterns invisible in isolation" width="800"/>

### デモ：複数ファイルにまたがるバグを発見する

```bash
copilot

> @samples/book-app-project/book_app.py @samples/book-app-project/books.py
>
> これらのファイルはどのように連携していますか？データフローはどうなっていますか？
```

> 💡 **応用オプション**: セキュリティに特化したクロスファイル分析を試したい場合は、Python のセキュリティ例を使ってみましょう：
> ```bash
> > @samples/buggy-code/python/user_service.py @samples/buggy-code/python/payment_processor.py
> > 両方のファイルにまたがるセキュリティ脆弱性を見つけてください
> ```

---

<details>
<summary>🎬 実際の動作を見てみましょう！</summary>

![Multi-File Demo](images/multi-file-demo.gif)

*デモの出力は異なる場合があります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

**Copilot CLI が発見する内容**：

```
クロスモジュール分析
=====================

1. データフローのパターン
   book_app.py が BookCollection インスタンスを作成してメソッドを呼び出す
   books.py が BookCollection クラスを定義してデータの永続化を管理する

   フロー: book_app.py (UI) → books.py (ビジネスロジック) → data.json (ストレージ)

2. 重複した表示関数
   book_app.py:9-21    show_books() 関数
   utils.py:28-36      print_books() 関数

   影響: ほぼ同じことをする2つの関数が存在します。一方を更新した場合
   （例えばフォーマットを変更した場合）、もう一方も忘れずに更新する必要があります。

3. 一貫性のないエラーハンドリング
   book_app.py は年変換の ValueError を処理する
   books.py はエラー時に暗黙的に None/False を返す

   パターン: モジュール間でエラーハンドリングの統一されたアプローチがない
```

**なぜこれが重要なのか**：単一ファイルのレビューでは全体像を見逃してしまいます。クロスファイル分析によってのみ、次のことが明らかになります：
- **重複コード**（統合が必要なもの）
- **データフローのパターン**（コンポーネントの連携方法）
- **アーキテクチャ上の問題**（保守性に影響するもの）

---

### デモ：60秒でコードベースを理解する

<img src="images/codebase-understanding.png" alt="Split-screen comparison showing manual code review taking 1 hour versus AI-assisted analysis taking 10 seconds" width="800" />

プロジェクトが初めてですか？Copilot CLI を使って素早く把握しましょう。

```bash
copilot

> @samples/book-app-project/
>
> このアプリが何をするか、そして最大の品質上の問題は何かを1段落で説明してください
```

**得られる結果**：
```
これは JSON ファイルに保存された本を追加、一覧表示、削除、検索できる CLI の本コレクションマネージャーです。最大の品質上の問題は次のとおりです：

1. 重複した表示ロジック - show_books() と print_books() が同じことをしている
2. 一貫性のないエラーハンドリング - 例外を発生させるものもあれば、False を返すものもある
3. 入力検証の欠如 - year が 0 になれる、title/author に空文字列が受け入れられる
4. テストの欠如 - find_book_by_title のような重要な関数にテストカバレッジがない

優先修正: 重複した表示関数を統合し、入力検証を追加する。
```

**成果**：1時間かかるコード読解が10秒に圧縮されます。どこに集中すべきかが明確になります。

---

## 実践的な例

### 例 1：コンテキストを使ったコードレビュー

```bash
copilot

> @samples/book-app-project/books.py このファイルの潜在的なバグをレビューしてください

# Copilot CLI はファイル全体の内容を把握し、具体的なフィードバックを提供します：
# "Line 49: Case-sensitive comparison may miss books..."
# "Line 29: JSON decode errors are caught but data corruption isn't logged..."

> @samples/book-app-project/book_app.py はどうですか？

# 今度は book_app.py をレビューしますが、books.py のコンテキストも保持されています
```

### 例 2：コードベースの理解

```bash
copilot

> @samples/book-app-project/books.py このモジュールは何をしますか？

# Copilot CLI が books.py を読み込み、BookCollection クラスを理解します

> @samples/book-app-project/ コード構造の概要を教えてください

# Copilot CLI がディレクトリをスキャンしてまとめます

> アプリはどのように本を保存・読み込みしますか？

# Copilot CLI はすでに確認したコードをトレースできます
```

<details>
<summary>🎬 マルチターン会話の実際の動作を見てみましょう！</summary>

![Multi-Turn Demo](images/multi-turn-demo.gif)

*デモの出力は異なる場合があります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

### 例 3：複数ファイルにまたがるリファクタリング

```bash
copilot

> @samples/book-app-project/book_app.py @samples/book-app-project/utils.py
> show_books() と print_books() という重複した表示関数があります。これらを統合するのを手伝ってください。

# Copilot CLI が両方のファイルを確認し、重複コードの統合方法を提案します
```

---

## セッション管理

セッションは作業中に自動的に保存されます。以前のセッションを再開して、中断した場所から続けることができます。

### セッションの自動保存

すべての会話は自動的に保存されます。通常どおり終了するだけです：

```bash
copilot

> @samples/book-app-project/ すべてのモジュール全体でエラーハンドリングを改善しましょう

[... 作業を行う ...]

> /exit
```

### 直近のセッションを再開する

```bash
# 中断した場所から続ける
copilot --continue
```

### 特定のセッションを再開する

```bash
# セッションの一覧から対話的に選択する
copilot --resume

# または特定のセッション ID を指定して再開する
copilot --resume abc123
```

> 💡 **セッション ID はどこで確認できますか？** 暗記する必要はありません。ID を指定せずに `copilot --resume` を実行すると、以前のセッションの一覧がセッション名、ID、最終アクティブ日時とともに表示されます。目的のセッションを選択するだけです。
>
> **複数のターミナルを使っている場合は？** 各ターミナルウィンドウはそれぞれ独自のコンテキストを持つ独立したセッションです。3つのターミナルで Copilot CLI を開いている場合、それぞれが別々のセッションになります。どのターミナルからでも `--resume` を実行すると、すべてのセッションを閲覧できます。`--continue` フラグは、どのターミナルで閉じたかにかかわらず、最後に閉じたセッションを取得します。
>
> **セッションを再起動せずに切り替えることはできますか？** はい、できます。アクティブなセッション内で `/resume` スラッシュコマンドを使用してください：
> ```
> > /resume
> # 切り替え先のセッション一覧が表示されます
> ```

### セッションを整理する

後で見つけやすいように、セッションに分かりやすい名前を付けましょう：

```bash
copilot

> /rename book-app-review
# セッションに識別しやすい名前が付きました
```

### コンテキストの確認と管理

ファイルや会話を追加するにつれ、Copilot CLI の[コンテキストウィンドウ](../GLOSSARY.md#context-window)が埋まっていきます。2つのコマンドで管理できます：

```bash
copilot

> /context
Context usage: 45,000 / 128,000 tokens (35%)

> /clear
# コンテキストを消去して新たに開始します。トピックを切り替えるときに使用してください
```

> 💡 **`/clear` を使うタイミング**: `books.py` をレビューしていて `utils.py` に話題を切り替えたい場合は、まず `/clear` を実行してください。そうしないと、古いトピックの不要なコンテキストがレスポンスを混乱させる可能性があります。

---

### 中断した場所から再開する

<img src="images/session-persistence-timeline.png" alt="Timeline showing how GitHub Copilot CLI sessions persist across days - start on Monday, resume on Wednesday with full context restored" width="800"/>

*セッションは終了時に自動保存されます。数日後に再開しても、ファイル、課題、進捗のすべてが記憶されています。*

複数日にまたがるワークフローを想像してみましょう：

```bash
# 月曜日：book app のレビューを開始する
copilot

> /rename book-app-review
> @samples/book-app-project/books.py
> すべてのコード品質の問題をレビューして番号を振ってください

Quality Issues Found:
1. Duplicate display functions (book_app.py & utils.py) - MEDIUM
2. No input validation for empty strings - MEDIUM
3. Year can be 0 or negative - LOW
4. No type hints on all functions - LOW
5. Missing error logging - LOW

> 問題 #1（重複した関数）を修正してください
# 修正作業...

> /exit
```

```bash
# 水曜日：中断した場所から正確に再開する
copilot --continue

> book app のレビューで未修正の問題は何ですか？

Remaining issues from our book-app-review session:
2. No input validation for empty strings - MEDIUM
3. Year can be 0 or negative - LOW
4. No type hints on all functions - LOW
5. Missing error logging - LOW

Issue #1 (duplicate functions) was fixed on Monday.

> 次に問題 #2 に取り組みましょう
```

**これが強力な理由**：数日後でも、Copilot CLI は次のことを覚えています：
- 作業していた正確なファイル
- 番号付きの課題リスト
- すでに対処済みの課題
- 会話のコンテキスト

再説明不要。ファイルの再読み込みも不要。そのまま作業を続けられます。

---

**🎉 これで基本をマスターしました！** `@` 構文、セッション管理（`--continue`/`--resume`/`/rename`）、コンテキストコマンド（`/context`/`/clear`）を使いこなすだけで、高い生産性を発揮できます。以降はオプションです。準備ができたときに戻ってきてください。

---

# オプション：より深く学ぶ

<img src="images/optional-going-deeper.png" alt="Abstract crystal cave in blue and purple tones representing deeper exploration of context concepts" width="800"/>

これらのトピックは上記の基本の上に構築されています。**興味のあるものを選ぶか、[練習](#practice)に進んでください。**

| 学びたい内容 | ジャンプ先 |
|---|---|
| ワイルドカードパターンと高度なセッションコマンド | [追加の @ パターンとセッションコマンド](#additional-patterns) |
| 複数のプロンプトをまたいでコンテキストを積み上げる | [コンテキストを意識した会話](#context-aware-conversations) |
| トークン制限と `/compact` | [コンテキストウィンドウの理解](#understanding-context-windows) |
| 参照するファイルの選び方 | [参照するものの選択](#choosing-what-to-reference) |
| スクリーンショットやモックアップの分析 | [画像の活用](#working-with-images) |

<details>
<summary><strong>追加の @ パターンとセッションコマンド</strong></summary>
<a id="additional-patterns"></a>

### 追加の @ パターン

上級ユーザー向けに、Copilot CLI はワイルドカードパターンや画像参照をサポートしています：

| パターン | 動作 |
|---------|--------------|
| `@folder/*.py` | フォルダ内のすべての .py ファイル |
| `@**/test_*.py` | 再帰的なワイルドカード：どこにあってもすべてのテストファイルを検索 |
| `@image.png` | UI レビュー用の画像ファイル |

```bash
copilot

> @samples/book-app-project/**/*.py にあるすべての TODO コメントを見つけてください
```

### セッション情報の確認

```bash
copilot

> /session
# 現在のセッションの詳細とワークスペースの概要を表示します

> /usage
# セッションのメトリクスと統計情報を表示します
```

### セッションを共有する

```bash
copilot

> /share file ./my-session.md
# セッションを markdown ファイルとしてエクスポートします

> /share gist
# セッションを含む GitHub gist を作成します
```

</details>

<details>
<summary><strong>コンテキストを意識した会話</strong></summary>
<a id="context-aware-conversations"></a>

### コンテキストを意識した会話

互いに積み上がるマルチターン会話を行うときに、真価が発揮されます。

#### 例：段階的な改善

```bash
copilot

> @samples/book-app-project/books.py BookCollection クラスをレビューしてください

Copilot CLI: "The class looks functional, but I notice:
1. Missing type hints on some methods
2. No validation for empty title/author
3. Could benefit from better error handling"

> すべてのメソッドに型ヒントを追加してください

Copilot CLI: "Here's the class with complete type hints..."
[Shows typed version]

> Now improve error handling

Copilot CLI: "Building on the typed version, here's improved error handling..."
[Adds validation and proper exceptions]

> Generate tests for this final version

Copilot CLI: "Based on the class with types and error handling..."
[Generates comprehensive tests]
```

各プロンプトが前の作業の上に構築されていることに注目してください。これがコンテキストの力です。

</details>

<details>
<summary><strong>コンテキストウィンドウの理解</strong></summary>
<a id="understanding-context-windows"></a>

### コンテキストウィンドウの理解

基本編で `/context` と `/clear` についてはすでに学んでいます。ここでは、コンテキストウィンドウの仕組みをより深く理解しましょう。

すべての AI には「コンテキストウィンドウ」があり、これは一度に考慮できるテキストの量です。

<img src="images/context-window-visualization.png" alt="Context Window Visualization" width="800"/>

*コンテキストウィンドウは机のようなものです：一度に置けるものには限りがあります。ファイル、会話履歴、システムプロンプトがすべてスペースを占有します。*

#### 上限に達したときの動作

```bash
copilot

> /context

Context usage: 45,000 / 128,000 tokens (35%)

# ファイルや会話を追加するにつれて増加します

> @large-codebase/

Context usage: 120,000 / 128,000 tokens (94%)

# 警告：コンテキスト制限に近づいています

> @another-large-file.py

Context limit reached. Older context will be summarized.
```

#### `/compact` コマンド

コンテキストが埋まってきても会話を失いたくない場合、`/compact` で履歴を要約してトークンを解放できます：

```bash
copilot

> /compact
# 会話履歴を要約し、コンテキストスペースを解放します
# 重要な発見や決定事項は保持されます
```

#### コンテキスト効率化のヒント

| 状況 | 対処 | 理由 |
|-----------|--------|-----|
| 新しいトピックを始める | `/clear` | 無関係なコンテキストを削除する |
| 長い会話 | `/compact` | 履歴を要約してトークンを解放する |
| 特定のファイルが必要 | `@folder/` ではなく `@file.py` | 必要なものだけを読み込む |
| 制限に達した | 新しいセッションを開始する | 新鮮な 128K コンテキスト |
| 複数のトピック | トピックごとに `/rename` を使用 | 正しいセッションに簡単に再開できる |

#### 大規模コードベースのベストプラクティス

1. **具体的に指定する**: `@samples/book-app-project/` ではなく `@samples/book-app-project/books.py`
2. **トピック間でクリアする**: フォーカスを切り替えるときは `/clear` を使用する
3. **`/compact` を活用する**: 会話を要約してコンテキストを解放する
4. **複数のセッションを使う**: 機能やトピックごとに 1 つのセッション

</details>

<details>
<summary><strong>参照するものの選択</strong></summary>
<a id="choosing-what-to-reference"></a>

### 参照するものの選択

コンテキストに関しては、すべてのファイルが同じ価値を持つわけではありません。賢く選択する方法を説明します：

#### ファイルサイズの考慮

| ファイルサイズ | 概算[トークン](../GLOSSARY.md#token)数 | 戦略 |
|-----------|-------------------|----------|
| 小（100行未満） | 約500〜1,500トークン | 自由に参照できます |
| 中（100〜500行） | 約1,500〜7,500トークン | 特定のファイルを参照します |
| 大（500行以上） | 7,500トークン以上 | 選択的に、特定のファイルを使用します |
| 非常に大（1,000行以上） | 15,000トークン以上 | 分割またはセクション指定を検討します |

**具体的な例：**
- book app の Python ファイル4つの合計 ≈ 2,000〜3,000トークン
- 一般的な Python モジュール（200行） ≈ 3,000トークン
- Flask API ファイル（400行） ≈ 6,000トークン
- package.json ≈ 200〜500トークン
- 短いプロンプト＋レスポンス ≈ 500〜1,500トークン

> 💡 **コードの簡単な見積もり方:** コードの行数に約15を掛けると、おおよそのトークン数になります。あくまで目安です。

#### 含めるべきものと除外すべきもの

**高い価値あり**（含めましょう）：
- エントリーポイント（`book_app.py`、`main.py`、`app.py`）
- 質問の対象となる特定のファイル
- 対象ファイルから直接インポートされているファイル
- 設定ファイル（`requirements.txt`、`pyproject.toml`）
- データモデルやデータクラス

**価値が低い**（除外を検討しましょう）：
- 生成されたファイル（コンパイル出力、バンドルされたアセット）
- Node modules やベンダーディレクトリ
- 大きなデータファイルやフィクスチャ
- 質問に関係のないファイル

#### 特定性のスペクトラム

```
Less specific ────────────────────────► More specific
@samples/book-app-project/                      @samples/book-app-project/books.py:47-52
     │                                       │
     └─ Scans everything                     └─ Just what you need
        (uses more context)                      (preserves context)
```

**広い指定が適切な場合**（`@samples/book-app-project/`）：
- 初期のコードベース探索
- 多くのファイルにまたがるパターンの検索
- アーキテクチャレビュー

**具体的な指定が適切な場合**（`@samples/book-app-project/books.py`）：
- 特定の問題のデバッグ
- 特定ファイルのコードレビュー
- 単一の関数についての質問

#### 実践例：段階的なコンテキスト読み込み

```bash
copilot

# ステップ 1：構造から始める
> @package.json What frameworks does this project use?

# ステップ 2：回答に基づいて絞り込む
> @samples/book-app-project/ Show me the project structure

# ステップ 3：重要な部分に集中する
> @samples/book-app-project/books.py BookCollection クラスをレビューしてください

# ステップ 4：必要に応じて関連ファイルを追加する
> @samples/book-app-project/book_app.py @samples/book-app-project/books.py How does the CLI use the BookCollection?
```

この段階的なアプローチにより、コンテキストを集中的かつ効率的に保てます。

</details>

<details>
<summary><strong>画像の活用</strong></summary>
<a id="working-with-images"></a>

### 画像の活用

`@` 構文を使って会話に画像を含めたり、**クリップボードから貼り付ける**（Cmd+V / Ctrl+V）ことができます。Copilot CLI はスクリーンショット、モックアップ、図を分析して、UI のデバッグ、デザインの実装、エラー分析に役立てることができます。

```bash
copilot

> @images/screenshot.png What is happening in this image?

> @images/mockup.png Write the HTML and CSS to match this design. Place it in a new file called index.html and put the CSS in styles.css.
```

> 📖 **詳細はこちら**: サポートされている形式、実践的なユースケース、画像とコードの組み合わせのヒントについては、[追加のコンテキスト機能](../appendices/additional-context.md#working-with-images)を参照してください。

</details>

---

# 練習

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

コンテキストとセッション管理のスキルを実践してみましょう。

---

## ▶️ 自分で試してみましょう

### プロジェクト全体のレビュー

このコースにはそのまま確認できるサンプルファイルが含まれています。copilot を起動して、次に示すプロンプトを実行してみましょう：

```bash
copilot

> @samples/book-app-project/ Give me a code quality review of this project

# Copilot CLI が次のような課題を特定します：
# - Duplicate display functions
# - Missing input validation
# - Inconsistent error handling
```

> 💡 **自分のファイルで試したい場合は？** 小さな Python プロジェクトを作成し（`mkdir -p my-project/src`）、いくつかの .py ファイルを追加して、`@my-project/src/` でレビューしてみましょう。サンプルコードを作成してもらいたい場合は copilot に依頼することもできます！

### セッションワークフロー

```bash
copilot

> /rename book-app-review
> @samples/book-app-project/books.py Let's add input validation for empty titles

[Copilot CLI が検証アプローチを提案します]

> Implement that fix
> Now consolidate the duplicate display functions in @samples/book-app-project/
> /exit

# 後で - 中断した場所から再開する
copilot --continue

> Generate tests for the changes we made
```

---

デモを完了したら、これらのバリエーションを試してみましょう：

1. **クロスファイルチャレンジ**: book_app.py と books.py がどのように連携しているかを分析します：
   ```bash
   copilot
   > @samples/book-app-project/book_app.py @samples/book-app-project/books.py
   > What's the relationship between these files? Are there any code smells?
   ```

2. **セッションチャレンジ**: セッションを開始し、`/rename my-first-session` で名前を付け、何かを作業し、`/exit` で終了してから `copilot --continue` を実行してみましょう。作業内容を覚えていますか？

3. **コンテキストチャレンジ**: セッションの途中で `/context` を実行してみましょう。トークンをどれくらい使っていますか？`/compact` を試してから再度確認してみましょう。（`/compact` の詳細については、「より深く学ぶ」の[コンテキストウィンドウの理解](#understanding-context-windows)を参照してください。）

**自己チェック**: `@folder/` が各ファイルを個別に開くよりも強力な理由を説明できれば、コンテキストを理解しています。

---

## 📝 課題

### メインチャレンジ：データフローのトレース

ハンズオンの例ではコード品質レビューと入力検証に焦点を当てました。今度は同じコンテキストスキルを別のタスク（アプリ内でのデータの流れを追うこと）に実践してみましょう：

1. インタラクティブセッションを開始します：`copilot`
2. `books.py` と `book_app.py` を同時に参照します：
   `@samples/book-app-project/books.py @samples/book-app-project/book_app.py Trace how a book goes from user input to being saved in data.json. What functions are involved at each step?`
3. 追加コンテキストとしてデータファイルを参照します：
   `@samples/book-app-project/data.json What happens if this JSON file is missing or corrupted? Which functions would fail?`
4. クロスファイルの改善点を尋ねます：
   `@samples/book-app-project/books.py @samples/book-app-project/utils.py Suggest a consistent error-handling strategy that works across both files.`
5. セッションの名前を変更します：`/rename data-flow-analysis`
6. `/exit` で終了し、`copilot --continue` で再開してデータフローに関するフォローアップ質問をしましょう

**成功の基準**: 複数のファイルにまたがるデータをトレースし、名前付きセッションを再開し、クロスファイルの提案を得られること。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**始め方：**
```bash
cd /path/to/copilot-cli-for-beginners
copilot
> @samples/book-app-project/books.py @samples/book-app-project/book_app.py Trace how a book goes from user input to being saved in data.json.
> @samples/book-app-project/data.json What happens if this file is missing or corrupted?
> /rename data-flow-analysis
> /exit
```

次に `copilot --continue` で再開します。

**便利なコマンド：**
- `@file.py` - 単一ファイルを参照する
- `@folder/` - フォルダ内のすべてのファイルを参照する（末尾の `/` に注意）
- `/context` - 使用中のコンテキスト量を確認する
- `/rename <name>` - 簡単に再開できるようにセッションに名前を付ける

</details>

### ボーナスチャレンジ：コンテキストの制限

1. `@samples/book-app-project/` ですべての book app ファイルを一度に参照します
2. 異なるファイル（`books.py`、`utils.py`、`book_app.py`、`data.json`）について詳細な質問をいくつかします
3. `/context` を実行して使用量を確認します。どれくらい早く埋まりますか？
4. `/compact` を使ってスペースを回収し、会話を続ける練習をします
5. ファイル参照をより具体的にして（例：フォルダ全体の代わりに `@samples/book-app-project/books.py`）、コンテキスト使用量への影響を確認します

---

<details>
<summary>🔧 <strong>よくある間違いとトラブルシューティング</strong>（クリックして展開）</summary>

### よくある間違い

| 間違い | 何が起きるか | 修正方法 |
|---------|--------------|-----|
| ファイル名の前に `@` を付け忘れる | Copilot CLI が「books.py」をプレーンテキストとして扱う | ファイルを参照するには `@samples/book-app-project/books.py` を使用する |
| セッションが自動的に継続すると期待する | 新たに `copilot` を起動すると以前のコンテキストが失われる | `--continue`（最後のセッション）または `--resume`（セッションを選択）を使用する |
| 現在のディレクトリ外のファイルを参照する | 「Permission denied」または「File not found」エラーが発生する | `/add-dir /path/to/directory` でアクセス権を付与する |
| トピックを切り替えるときに `/clear` を使わない | 古いコンテキストが新しいトピックのレスポンスを混乱させる | 別のタスクを始める前に `/clear` を実行する |

### トラブルシューティング

**「File not found」エラー** - 正しいディレクトリにいることを確認してください：

```bash
pwd  # 現在のディレクトリを確認する
ls   # ファイルを一覧表示する

# その後 copilot を起動し、相対パスを使用する
copilot

> Review @samples/book-app-project/books.py
```

**「Permission denied」** - 許可リストにディレクトリを追加してください：

```bash
copilot --add-dir /path/to/directory

# またはセッション内で：
> /add-dir /path/to/directory
```

**コンテキストが早く埋まりすぎる場合**：
- ファイル参照をより具体的にする
- 異なるトピック間で `/clear` を使用する
- 作業を複数のセッションに分割する

</details>

---

# まとめ

## 🔑 重要なポイント

1. **`@` 構文**により、Copilot CLI にファイル、ディレクトリ、画像のコンテキストを提供できます
2. **マルチターン会話**は、コンテキストが蓄積されるにつれて互いに積み上がります
3. **セッションは自動保存**されます：`--continue` または `--resume` を使って中断した場所から再開できます
4. **コンテキストウィンドウ**には制限があります：`/context`、`/clear`、`/compact` で管理しましょう
5. **権限フラグ**（`--add-dir`、`--allow-all`）は複数ディレクトリへのアクセスを制御します。慎重に使用してください！
6. **画像参照**（`@screenshot.png`）は UI の問題を視覚的にデバッグするのに役立ちます

> 📚 **公式ドキュメント**: [Use Copilot CLI](https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli) でコンテキスト、セッション、ファイルの操作に関する完全なリファレンスを確認できます。

> 📋 **クイックリファレンス**: コマンドとショートカットの完全な一覧は [GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)を参照してください。

---

## ➡️ 次のステップ

Copilot CLI にコンテキストを提供する方法を習得しました。次は実際の開発タスクに活用してみましょう。ここで学んだコンテキストのテクニック（ファイル参照、クロスファイル分析、セッション管理）は、次章の強力なワークフローの基盤となります。

**[第3章：開発ワークフロー](../03-development-workflows/README.md)**では次のことを学びます：

- コードレビューのワークフロー
- リファクタリングパターン
- デバッグのサポート
- テスト生成
- Git との連携

---

**[← 第1章に戻る](../01-setup-and-first-steps/README.md)** | **[第3章へ進む →](../03-development-workflows/README.md)**
