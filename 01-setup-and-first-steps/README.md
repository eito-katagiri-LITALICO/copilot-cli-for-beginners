![Chapter 01: First Steps](images/chapter-header.png)

> **AI がバグを即座に発見し、複雑なコードを説明し、動作するスクリプトを生成する様子をご覧ください。そして、GitHub Copilot CLI を使う3つの方法を学びましょう。**

この章からいよいよ魔法が始まります！GitHub Copilot CLI を「スピードダイヤルで呼べるシニアエンジニア」と表現する開発者が多い理由を、実際に体験していただけます。AI がセキュリティバグを数秒で発見し、複雑なコードをわかりやすい言葉で説明し、動作するスクリプトを即座に生成する様子をご覧ください。そして、3つのインタラクションモード（Interactive、Plan、Programmatic）をマスターして、どのタスクにどのモードを使えばよいかを理解しましょう。

> ⚠️ **前提条件**: 先に **[Chapter 00: Quick Start](../00-quick-start/README.md)** を完了していることを確認してください。以下のデモを実行する前に、GitHub Copilot CLI がインストールされ、認証が完了している必要があります。

## 🎯 学習目標

この章を終えると、以下のことができるようになります。

- ハンズオンデモを通じて GitHub Copilot CLI が提供する生産性の向上を体験できる
- タスクに応じて適切なモード（Interactive、Plan、Programmatic）を選択できる
- スラッシュコマンドを使ってセッションをコントロールできる

> ⏱️ **推定時間**: 約45分（読書15分 + ハンズオン30分）

---

# 初めての Copilot CLI 体験

<img src="images/first-copilot-experience.png" alt="デスクに座ってモニターのコードを見ている開発者と、AI アシスタンスを表すキラキラした粒子" width="800"/>

さっそく Copilot CLI で何ができるか試してみましょう。

---

## はじめの一歩：最初のプロンプト

印象的なデモに入る前に、今すぐ試せるシンプルなプロンプトから始めましょう。**コードリポジトリは不要です**！ターミナルを開いて Copilot CLI を起動するだけです。

```bash
copilot
```

初心者向けのプロンプトをいくつか試してみてください。

```
> Python の dataclass を簡単な言葉で説明してください

> 特定のキーで辞書のリストをソートする関数を書いてください

> Python のリストとタプルの違いは何ですか？

> きれいな Python コードを書くためのベストプラクティスを5つ教えてください
```

Python を使っていない方もご安心ください！お好みの言語について質問するだけです。

どれほど自然に使えるかに気づくはずです。同僚に話しかけるように質問するだけです。探索が終わったら、`/exit` と入力してセッションを終了しましょう。

**重要なポイント**: GitHub Copilot CLI は会話形式で使えます。始めるために特別な構文は必要ありません。ただ普通の言葉で質問するだけです。

## 実際の動作を見てみましょう

開発者が「スピードダイヤルで呼べるシニアエンジニア」と表現する理由を見ていきましょう。

> 📖 **例の読み方**: `>` で始まる行は、Copilot CLI の対話セッション内で入力するプロンプトです。`>` プレフィックスのない行は、ターミナルで実行するシェルコマンドです。

> 💡 **出力例について**: このコースで示されているサンプル出力はあくまで例示です。Copilot CLI の回答は毎回異なるため、実際の結果は文言、フォーマット、詳細レベルが異なります。返ってくる情報の*種類*に注目してください。正確なテキストにこだわる必要はありません。

### デモ1：数秒でコードレビュー

このコースには、意図的なコード品質の問題を含むサンプルファイルが含まれています。そのファイルをレビューしてみましょう。

```bash
# Clone the course repository if you're working locally and haven't already
git clone https://github.com/github/copilot-cli-for-beginners
cd copilot-cli-for-beginners

# Start Copilot
copilot
```

対話セッション内で次を実行します。

```
> @samples/book-app-project/book_app.py のコード品質の問題をレビューして、改善案を提案してください
```

> 💡 **`@` とは何ですか？** `@` シンボルは Copilot CLI にファイルを読み込むよう指示します。詳細は Chapter 02 で学びます。今はコマンドをそのままコピーしてください。

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Code Review Demo](images/code-review-demo.gif)

*デモの出力は異なります。お使いのモデル、ツール、回答はここに示されたものと異なる場合があります。*

</details>

---

**まとめ**: プロのコードレビューが数秒で完了します。手動でのレビューにかかる時間と比べてみてください！

---

### デモ2：複雑なコードを解説

コードを見てもよくわからないと思ったことはありませんか？Copilot CLI セッションで試してみましょう。

```
> @samples/book-app-project/books.py が何をするか、わかりやすく説明してください
```

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Explain Code Demo](images/explain-code-demo.gif)

*デモの出力は異なります。お使いのモデル、ツール、回答はここに示されたものと異なる場合があります。*

</details>

---

**実行結果**（実際の出力は異なります）: Copilot CLI がファイルを読み込み、コードを理解し、わかりやすい言葉で説明してくれます。

```
This is a book collection management module using Python dataclasses.

Think of it like a digital bookshelf:
- Book is a dataclass - a simple way to store book information (title, author, year, read status)
- BookCollection manages the entire collection and handles saving/loading

Key components:

1. @dataclass decorator (line 8)
   - Automatically creates __init__, __repr__, etc.
   - Clean way to define data structures in Python

2. BookCollection class (line 16)
   - Maintains a list of Book objects
   - Handles persistence with JSON file I/O
   - load_books() reads from data.json
   - save_books() writes to data.json using asdict()

3. Book operations:
   - add_book() - creates and saves new books
   - find_book_by_title() - searches collection
   - mark_as_read() - updates read status
   - find_by_author() - filters by author name

Common pattern: Read from JSON → Work with Python objects → Write back to JSON
```

**まとめ**: 忍耐強いメンターが説明してくれるように、複雑なコードをわかりやすく解説してもらえます。

---

### デモ3：動作するコードを生成

15分かけて検索しなければならないような関数も、セッション内でリクエストするだけです。

```
> 本のリストを受け取り、合計数・既読数・未読数・最古と最新の本の統計を返す Python 関数を書いてください
```

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Generate Code Demo](images/generate-code-demo.gif)

*デモの出力は異なります。お使いのモデル、ツール、回答はここに示されたものと異なる場合があります。*

</details>

---

**実行結果**: すぐに使えるコピー＆ペースト可能な完全なコードが数秒で生成されます。

探索が終わったら、セッションを終了しましょう。

```
> /exit
```

**まとめ**: 即座に結果が得られ、しかも同じ継続セッションの中で完結します。

---

# モードとコマンド

<img src="images/modes-and-commands.png" alt="Copilot CLI のモードとコマンドを表す、光り輝くスクリーンやダイヤル、イコライザーが並ぶ近未来的なコントロールパネル" width="800"/>

Copilot CLI で何ができるかを確認しました。次は、これらの機能を効果的に使う方法を理解しましょう。鍵となるのは、状況に応じて3つのインタラクションモードのどれを使うかを知ることです。

> 💡 **注意**: Copilot CLI には、入力を待たずにタスクをこなす **Autopilot** モードもあります。強力ですが、完全な権限の付与が必要で、プレミアムリクエストを自律的に使用します。このコースでは以下の3つのモードに焦点を当てます。基本に慣れたら Autopilot の使い方もご紹介します。

---

## 🧩 現実世界のアナロジー：外食

GitHub Copilot CLI の使い方は、外食に行くことに例えられます。お店への道から注文まで、状況によって適切なアプローチが異なります。

| モード | 外食のアナロジー | 使用場面 |
|------|----------------|-------------|
| **Plan** | レストランへの GPS ルート案内 | 複雑なタスク - ルートを確認し、経由地を確認し、計画に合意してから出発する |
| **Interactive** | ウェイターとの会話 | 探索と反復 - 質問し、カスタマイズし、リアルタイムのフィードバックを受ける |
| **Programmatic** | ドライブスルーでの注文 | 素早く具体的なタスク - 自分の環境にとどまって素早く結果を得る |

外食と同じように、どのアプローチがいつ適しているかは自然に身につきます。

<img src="images/ordering-food-analogy.png" alt="GitHub Copilot CLI を使う3つの方法 - Plan モード（レストランへの GPS ルート）、Interactive モード（ウェイターとの会話）、Programmatic モード（ドライブスルー）" width="800"/>

*タスクに応じてモードを選択してください：Plan は先に計画を立てるとき、Interactive は双方向のコラボレーションに、Programmatic は素早く一回限りの結果が欲しいときに*

### どのモードから始めるべきか

**Interactive モードから始めましょう。**
- 試行錯誤しながら追加質問ができます
- 会話を通じて自然にコンテキストが積み重なります
- `/clear` で簡単にやり直せます

慣れてきたら以下も試してみましょう。
- **Programmatic モード** (`copilot -p "<プロンプト>"`) — 素早い一回限りの質問に
- **Plan モード** (`/plan`) — コーディング前により詳細に計画を立てたいときに

---

## 3つのモード

### モード1：Interactive モード（まずここから）

<img src="images/interactive-mode.png" alt="Interactive モード - 質問に答えたり注文を調整したりできるウェイターとの会話のように" width="250"/>

**最適な用途**: 探索、反復、複数ターンの会話。質問に答え、フィードバックを受け取り、その場で注文を調整できるウェイターとの会話のようなものです。

対話セッションを開始します。

```bash
copilot
```

ここまで見てきたように、自由に入力できるプロンプトが表示されます。利用可能なコマンドのヘルプを表示するには、次を入力してください。

```
> /help
```

**重要なポイント**: Interactive モードはコンテキストを維持します。実際の会話のように、各メッセージが前のメッセージの上に積み重なります。

#### Interactive モードの例

```bash
copilot

> @samples/book-app-project/utils.py をレビューして改善案を提案してください

> すべての関数に型ヒントを追加してください

> エラーハンドリングをより堅牢にしてください

> /exit
```

各プロンプトが前の回答の上に積み上がっていることに注目してください。毎回最初からではなく、会話を続けているのです。

---

### モード2：Plan モード

<img src="images/plan-mode.png" alt="Plan モード - GPS を使って旅行前にルートを計画するように" width="250"/>

**最適な用途**: 実行前にアプローチを確認したい複雑なタスク。GPS を使って旅行前にルートを計画することに似ています。

Plan モードでは、コードを書く前にステップバイステップの計画を作成します。`/plan` コマンドを使うか、**Shift+Tab** を押して Plan モードに切り替えてください。

> 💡 **ヒント**: **Shift+Tab** を押すとモードが切り替わります：Interactive → Plan → Autopilot。対話セッション中にいつでも押してコマンドを入力せずにモードを切り替えられます。

```bash
copilot

> /plan book app に「既読にする」コマンドを追加する
```

**Plan モードの出力例**（実際の出力は異なる場合があります）:

```
📋 実装計画

ステップ 1: book_app.py のコマンドハンドラーを更新する
  - "mark" コマンド用の新しい elif ブランチを追加する
  - handle_mark_as_read() 関数を作成する

ステップ 2: ハンドラー関数を実装する
  - ユーザーに本のタイトルを入力させる
  - collection.mark_as_read(title) を呼び出す
  - 成功/失敗メッセージを表示する

ステップ 3: ヘルプテキストを更新する
  - 利用可能なコマンド一覧に "mark" を追加する
  - コマンドの使い方を説明する

ステップ 4: フローをテストする
  - 本を追加する
  - 既読にする
  - リスト出力でステータスが変わることを確認する

実装を進めますか？ [Y/n]
```

**重要なポイント**: Plan モードでは、コードが書かれる前にアプローチを確認・修正できます。計画が完成したら、後で参照するためにファイルに保存するよう Copilot CLI に指示することもできます。例えば「Save this plan to `mark_as_read_plan.md`」と入力すると、計画の詳細を含む markdown ファイルが作成されます。

> 💡 **もっと複雑なタスクを試したいですか？** `/plan Add search and filter capabilities to the book app` を試してみてください。Plan モードはシンプルな機能からフルアプリケーションまでスケールします。

> 📚 **Autopilot モード**: Shift+Tab を押すと **Autopilot** と呼ばれる3番目のモードに切り替わることに気づいたかもしれません。Autopilot モードでは、各ステップ後に入力を待たずに計画全体を実行します — タスクを同僚に渡して「終わったら教えて」と言うようなものです。一般的なワークフローは plan → accept → autopilot で、まず計画を上手に作れるようになる必要があります。Interactive モードと Plan モードに慣れてから、準備ができたら[公式ドキュメント](https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot)をご確認ください。

---

### モード3：Programmatic モード

<img src="images/programmatic-mode.png" alt="Programmatic モード - ウェイターと話さずに素早く注文するドライブスルーのように" width="250"/>

**最適な用途**: 自動化、スクリプト、CI/CD、単発のコマンド。ウェイターと話さずに素早く注文するドライブスルーのようなものです。

インタラクションを必要としない一回限りのコマンドには `-p` フラグを使用します。

```bash
# Generate code
copilot -p "Write a function that checks if a number is even or odd"

# Get quick help
copilot -p "How do I read a JSON file in Python?"
```

**重要なポイント**: Programmatic モードは素早く回答して終了します。会話はなく、入力 → 出力だけです。

<details>
<summary>📚 <strong>発展：スクリプトでの Programmatic モードの活用</strong>（クリックして展開）</summary>

慣れてきたら、シェルスクリプト内で `-p` を使用できます。

```bash
#!/bin/bash

# Generate commit messages automatically
COMMIT_MSG=$(copilot -p "Generate a commit message for: $(git diff --staged)")
git commit -m "$COMMIT_MSG"

# Review a file
copilot --allow-all -p "Review @myfile.py for issues"
```
> ⚠️ **`--allow-all` について**: このフラグはすべての権限プロンプトをスキップし、Copilot CLI がファイルの読み取り、コマンドの実行、URL へのアクセスを確認なしに行えるようにします。これは、アクションを承認するための対話セッションがない Programmatic モード（`-p`）で必要です。自分で記述したプロンプトで、信頼できるディレクトリでのみ `--allow-all` を使用してください。信頼できない入力や機密性の高いディレクトリでは絶対に使用しないでください。

</details>

---

## 必須のスラッシュコマンド

これらのコマンドは Interactive モードで使用します。**まずはこの6つだけで十分**です。日常使用の90%をカバーしています。

| コマンド | 機能 | 使用タイミング |
|---------|--------------|-------------|
| `/help` | 利用可能なコマンドをすべて表示する | コマンドを忘れたとき |
| `/clear` | 会話をクリアして最初からやり直す | 話題を切り替えるとき |
| `/plan` | コーディング前に作業を計画する | より複雑な機能に対して |
| `/research` | GitHub とウェブソースを使った詳細なリサーチ | コーディング前にトピックを調査するとき |
| `/model` | AI モデルの表示または切り替え | AI モデルを変更したいとき |
| `/exit` | セッションを終了する | 作業が終わったとき |

始める際に必要なのはこれだけです！慣れてきたら追加のコマンドを探索できます。

> 📚 **公式ドキュメント**: コマンドとフラグの完全なリストは [CLI コマンドリファレンス](https://docs.github.com/copilot/reference/cli-command-reference)をご覧ください。

<details>
<summary>📚 <strong>追加コマンド</strong>（クリックして展開）</summary>

> 💡 上記の必須コマンドは日常使用の多くをカバーしています。このリファレンスは、さらに探索する準備ができたときのためにあります。

### Agent 環境

| コマンド | 機能 |
|---------|--------------|
| `/init` | リポジトリの Copilot 指示を初期化する |
| `/agent` | 利用可能な agent を参照して選択する |
| `/skills` | 拡張機能のための skill を管理する |
| `/mcp` | MCP サーバーの設定を管理する |

> 💡 skill の詳細は [Chapter 05](../05-skills/README.md) で、MCP サーバーは [Chapter 06](../06-mcp-servers/README.md) で説明します。

### モデルとサブ Agent

| コマンド | 機能 |
|---------|--------------|
| `/model` | AI モデルの表示または切り替え |
| `/delegate` | GitHub の Copilot coding agent にタスクを委任する（クラウドの agent） |
| `/fleet` | 複雑なタスクを並行サブタスクに分割して高速化する |
| `/tasks` | バックグラウンドのサブ agent とデタッチされたシェルセッションを表示する |

### コード

| コマンド | 機能 |
|---------|--------------|
| `/diff` | 現在のディレクトリの変更をレビューする |
| `/pr` | 現在のブランチのプルリクエストを操作する |
| `/review` | コードレビュー agent を実行して変更を分析する |
| `/research` | GitHub とウェブソースを使った詳細なリサーチを実行する |
| `/terminal-setup` | 複数行入力のサポートを有効化する（shift+enter と ctrl+enter） |

### 権限

| コマンド | 機能 |
|---------|--------------|
| `/allow-all` | このセッションのすべての権限プロンプトを自動承認する |
| `/add-dir <directory>` | 許可リストにディレクトリを追加する |
| `/list-dirs` | 許可されたすべてのディレクトリを表示する |
| `/cwd`, `/cd [directory]` | 作業ディレクトリを表示または変更する |

> ⚠️ **使用には注意**: `/allow-all` は確認プロンプトをスキップします。信頼できるプロジェクトでは便利ですが、信頼できないコードには注意してください。

### セッション

| コマンド | 機能 |
|---------|--------------|
| `/resume` | 別のセッションに切り替える（セッション ID を指定することもできます） |
| `/rename` | 現在のセッションの名前を変更する |
| `/context` | コンテキストウィンドウのトークン使用量と可視化を表示する |
| `/usage` | セッションの使用状況メトリクスと統計を表示する |
| `/session` | セッション情報とワークスペースのサマリーを表示する |
| `/compact` | コンテキスト使用量を削減するために会話を要約する |
| `/share` | セッションを markdown ファイルまたは GitHub gist としてエクスポートする |

### ヘルプとフィードバック

| コマンド | 機能 |
|---------|--------------|
| `/help` | 利用可能なコマンドをすべて表示する |
| `/changelog` | CLI バージョンの変更ログを表示する |
| `/feedback` | GitHub にフィードバックを送信する |
| `/theme` | ターミナルのテーマを表示または設定する |

### クイックシェルコマンド

`!` を先頭に付けることで、AI を介さずにシェルコマンドを直接実行できます。

```bash
copilot

> !git status
# Runs git status directly, bypassing the AI

> !python -m pytest tests/
# Runs pytest directly
```

### モデルの切り替え

Copilot CLI は OpenAI、Anthropic、Google などの複数の AI モデルをサポートしています。利用可能なモデルはサブスクリプションレベルと地域によって異なります。`/model` を使ってオプションを確認し、切り替えることができます。

```bash
copilot
> /model

# Shows available models and lets you pick one. Select Sonnet 4.5.
```

> 💡 **ヒント**: モデルによって消費する「プレミアムリクエスト」の量が異なります。**1x** と表示されているモデル（Claude Sonnet 4.5 など）は優れたデフォルト選択です。高性能で効率的です。乗数が高いモデルはプレミアムリクエストのクォータを早く消費するため、本当に必要なときのために取っておきましょう。

</details>

---

# 練習

<img src="../images/practice.png" alt="コードが表示されたモニター、ランプ、コーヒーカップ、ヘッドフォンが置かれたハンズオン練習の準備が整った温かみのあるデスクの設定" width="800"/>

学んだことを実際に試してみましょう。

---

## ▶️ 自分で試してみよう

### 対話的な探索

Copilot を起動して、フォローアップのプロンプトを使って book app を反復的に改善しましょう。

```bash
copilot

> @samples/book-app-project/book_app.py をレビューしてください。どこを改善できますか？

> if/elif チェーンをより保守しやすい構造にリファクタリングしてください

> すべてのハンドラー関数に型ヒントを追加してください

> /exit
```

### 機能を計画する

`/plan` を使って、コードを書く前に Copilot CLI に実装のマップを作成させましょう。

```bash
copilot

> /plan タイトルまたは著者で本を検索できる機能を book app に追加する

# Review the plan
# Approve or modify
# Watch it implement step by step
```

### Programmatic モードで自動化する

`-p` フラグを使うと、Interactive モードに入らずにターミナルから直接 Copilot CLI を実行できます。以下のスクリプトをリポジトリルートからターミナル（Copilot の中ではなく）にコピー＆ペーストして、book app のすべての Python ファイルをレビューしてください。

```bash
# Review all Python files in the book app
for file in samples/book-app-project/*.py; do
  echo "Reviewing $file..."
  copilot --allow-all -p "Quick code quality review of @$file - critical issues only"
done
```

**PowerShell (Windows):**

```powershell
# Review all Python files in the book app
Get-ChildItem samples/book-app-project/*.py | ForEach-Object {
  $relativePath = "samples/book-app-project/$($_.Name)";
  Write-Host "Reviewing $relativePath...";
  copilot --allow-all -p "Quick code quality review of @$relativePath - critical issues only" 
}
```

---

デモを完了したら、以下のバリエーションを試してみてください。

1. **Interactive チャレンジ**: `copilot` を起動して book app を探索してください。`@samples/book-app-project/books.py` について質問し、3回連続で改善をリクエストしてみましょう。

2. **Plan モードチャレンジ**: `/plan book app に評価とレビュー機能を追加する` を実行してください。計画をよく読んで確認してください。意味が通っていますか？

3. **Programmatic チャレンジ**: `copilot --allow-all -p "@samples/book-app-project/book_app.py のすべての関数を列挙し、それぞれの機能を説明してください"` を実行してください。一発で動作しましたか？

---

## 📝 課題

### メインチャレンジ：Book App ユーティリティの改善

ハンズオンの例では `book_app.py` のレビューとリファクタリングに焦点を当てました。今度は別のファイル `utils.py` で同じスキルを練習しましょう。

1. 対話セッションを開始する: `copilot`
2. Copilot CLI にファイルの概要を説明させる: `@samples/book-app-project/utils.py このファイルの各関数は何をしますか？`
3. 入力バリデーションの追加を依頼する: "get_user_choice() に空の入力や数字以外の入力を処理するバリデーションを追加してください"
4. エラーハンドリングの改善を依頼する: "get_book_details() がタイトルに空文字を受け取った場合はどうなりますか？そのガードを追加してください。"
5. docstring の追加を依頼する: "get_book_details() にパラメータの説明と戻り値を含む包括的な docstring を追加してください"
6. プロンプト間でコンテキストがどのように引き継がれるかを観察してください。各改善が前の改善の上に積み重なります
7. `/exit` で終了する

**成功基準**: 複数ターンの会話を通じて、入力バリデーション、エラーハンドリング、docstring が追加された改善済みの `utils.py` が完成しているはずです。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**試してみるサンプルプロンプト:**
```bash
> @samples/book-app-project/utils.py このファイルの各関数は何をしますか？
> get_user_choice() に空の入力や数字以外の入力を処理するバリデーションを追加してください
> get_book_details() がタイトルに空文字を受け取った場合はどうなりますか？そのガードを追加してください。
> get_book_details() にパラメータの説明と戻り値を含む包括的な docstring を追加してください
```

**よくある問題:**
- Copilot CLI が確認の質問をしてきた場合は、自然に答えてください
- コンテキストは引き継がれるため、各プロンプトは前のプロンプトの上に積み重なります
- やり直したい場合は `/clear` を使用してください

</details>

### ボーナスチャレンジ：モードを比較する

例では検索機能に `/plan` を、バッチレビューに `-p` を使いました。今度は単一の新しいタスクで3つのモードすべてを試してみましょう：`BookCollection` クラスへの `list_by_year()` メソッドの追加です。

1. **Interactive**: `copilot` → メソッドを段階的に設計・構築するよう依頼する
2. **Plan**: `/plan 出版年の範囲でフィルタリングする list_by_year(start, end) メソッドを BookCollection に追加する`
3. **Programmatic**: `copilot --allow-all -p "@samples/book-app-project/books.py start と end の年を含む範囲で出版された本を返す list_by_year(start, end) メソッドを追加してください"`

**振り返り**: どのモードが最も自然に感じられましたか？それぞれいつ使いますか？

---

<details>
<summary>🔧 <strong>よくある間違いとトラブルシューティング</strong>（クリックして展開）</summary>

### よくある間違い

| 間違い | 何が起こるか | 修正方法 |
|---------|--------------|-----|
| `/exit` の代わりに `exit` と入力する | Copilot CLI が「exit」をコマンドではなくプロンプトとして扱う | スラッシュコマンドは常に `/` で始める |
| 複数ターンの会話に `-p` を使う | 各 `-p` 呼び出しは独立しており、以前の呼び出しの記憶がない | コンテキストを積み重ねる会話には Interactive モード（`copilot`）を使う |
| `$` や `!` を含むプロンプトのクォートを忘れる | Copilot CLI が受け取る前にシェルが特殊文字を解釈する | プロンプトをクォートで囲む: `copilot -p "What does $HOME mean?"` |

### トラブルシューティング

**「Model not available」** — ご使用のサブスクリプションにすべてのモデルが含まれていない場合があります。`/model` で利用可能なモデルを確認してください。

**「Context too long」** — 会話でコンテキストウィンドウが最大まで使用されました。`/clear` でリセットするか、新しいセッションを開始してください。

**「Rate limit exceeded」** — 数分待ってから再試行してください。バッチ操作には遅延を入れた Programmatic モードの使用を検討してください。

</details>

---

# まとめ

## 🔑 重要なポイント

1. **Interactive モード**は探索と反復のためのモードです - コンテキストが引き継がれます。これまで話してきた内容を覚えている人と会話しているようなものです。
2. **Plan モード**は通常、より複雑なタスクに使用します。実装前にレビューしましょう。
3. **Programmatic モード**は自動化のためのモードです。インタラクションは必要ありません。
4. **4つの必須コマンド**（`/help`、`/clear`、`/plan`、`/exit`）で日常使用のほとんどをカバーできます。

> 📋 **クイックリファレンス**: コマンドとショートカットの完全なリストは [GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)をご覧ください。

---

## ➡️ 次のステップ

3つのモードを理解しました。次は Copilot CLI にコードのコンテキストを提供する方法を学びましょう。

**[Chapter 02: Context and Conversations](../02-context-conversations/README.md)** では以下を学びます。

- ファイルやディレクトリを参照するための `@` 構文
- `--resume` と `--continue` を使ったセッション管理
- コンテキスト管理が Copilot CLI を真に強力にする仕組み

---

**[← コースホームに戻る](../README.md)** | **[Chapter 02 へ進む →](../02-context-conversations/README.md)**
