![Chapter 06: MCP Servers](images/chapter-header.png)

> **Copilot が GitHub のイシューを読み取り、データベースを確認し、ターミナルから PR を作成できたら、どうでしょうか？**

これまで Copilot は、`@` で参照したファイル、会話の履歴、自身のトレーニングデータなど、直接渡された情報しか扱うことができませんでした。しかし、Copilot 自身が GitHub リポジトリを確認したり、プロジェクトファイルを参照したり、ライブラリの最新ドキュメントを調べたりできるとしたらどうでしょうか？

それを実現するのが MCP（Model Context Protocol）です。MCP は、Copilot を外部サービスに接続して、リアルタイムの実際のデータにアクセスできるようにする仕組みです。Copilot が接続する各サービスを「MCP server」と呼びます。この章では、いくつかの接続を設定して、Copilot がいかに強力になるかを体験します。

> 💡 **MCP をすでにご存じの方は？** [クイックスタート](#-use-the-built-in-github-mcp) へ進んで動作確認をしてから、server の設定を始めてください。

## 🎯 学習目標

この章を終えると、以下のことができるようになります。

- MCP とは何か、なぜ重要なのかを理解する
- `/mcp` コマンドを使って MCP server を管理する
- GitHub、ファイルシステム、ドキュメント用の MCP server を設定する
- book app プロジェクトで MCP を活用したワークフローを使用する
- カスタム MCP server をいつ・どのように構築するかを把握する（任意）

> ⏱️ **想定所要時間**：約 50 分（読書 15 分 + ハンズオン 35 分）

---

## 🧩 実世界のアナロジー：ブラウザの拡張機能

<img src="images/browser-extensions-analogy.png" alt="MCP Servers are like Browser Extensions" width="800"/>

MCP server はブラウザの拡張機能に例えることができます。ブラウザ単体でも Web ページを表示できますが、拡張機能を使うと追加のサービスに接続できます。

| ブラウザの拡張機能 | 接続先 | MCP での対応 |
|-------------------|---------------------|----------------|
| パスワードマネージャー | パスワードの保管庫 | **GitHub MCP** → リポジトリ、イシュー、PR |
| Grammarly | 文章解析サービス | **Context7 MCP** → ライブラリのドキュメント |
| ファイルマネージャー | クラウドストレージ | **Filesystem MCP** → ローカルのプロジェクトファイル |

拡張機能がなくてもブラウザは便利ですが、拡張機能を追加することで格段に強力になります。MCP server は Copilot に対して同じことをしてくれます。Copilot をリアルなデータソースに接続することで、GitHub のイシューを読んだり、ファイルシステムを探索したり、最新のドキュメントを取得したりできるようになります。

***MCP server は Copilot を外の世界、つまり GitHub、リポジトリ、ドキュメントなどにつなぎます***

> �� **重要なポイント**：MCP がない状態では、Copilot は `@` で明示的に共有したファイルしか見ることができません。MCP があると、プロジェクトを自発的に探索したり、GitHub リポジトリを確認したり、ドキュメントを調べたりすることが、すべて自動的に行われます。

---

<img src="images/quick-start-mcp.png" alt="Power cable connecting with bright electrical spark surrounded by floating tech icons representing MCP server connections" width="800"/>

# クイックスタート：30 秒で MCP を体験

## 組み込みの GitHub MCP server を使ってみる
設定をする前に、今すぐ MCP を実際に体験してみましょう。
GitHub MCP server はデフォルトで含まれています。次を試してください。

```bash
copilot
> このリポジトリの最近のコミットを一覧表示してください
```

Copilot が実際のコミットデータを返したなら、MCP の動作を確認できました。これが GitHub MCP server があなたの代わりに GitHub にアクセスしている様子です。しかし GitHub は *1 つ* の server に過ぎません。この章では、さらに多くの server（ファイルシステムアクセス、最新のドキュメントなど）を追加する方法を紹介します。

---

## `/mcp show` コマンド

`/mcp show` を使うと、設定済みの MCP server と、それらが有効かどうかを確認できます。

```bash
copilot

> /mcp show

MCP Servers:
✓ github (enabled) - GitHub integration
✓ filesystem (enabled) - File system access
```

> 💡 **GitHub server しか表示されない場合は？** それで正常です！追加の MCP server をまだ設定していなければ、GitHub だけが表示されます。次のセクションで追加します。

> 📚 **すべての `/mcp` コマンドを確認したい場合は？** server の追加、編集、有効化、削除などのコマンドが用意されています。この章の末尾にある[コマンドリファレンス](#-additional-mcp-commands)をご覧ください。

<details>
<summary>🎬 実際の動作を確認する！</summary>

![MCP Status Demo](images/mcp-status-demo.gif)

*デモの出力は異なる場合があります。使用するモデル、ツール、レスポンスはここで示されたものと異なります。*

</details>

---

## MCP による違い

MCP を使うと実際にどう変わるかを示します。

**MCP なし：**
```bash
> GitHub のイシュー #42 の内容は何ですか？

"I don't have access to GitHub. You'll need to copy and paste the issue content."
```

**MCP あり：**
```bash
> このリポジトリのイシュー #42 の内容は何ですか？

Issue #42: Login fails with special characters
Status: Open
Labels: bug, priority-high
Description: Users report that passwords containing...
```

MCP によって、Copilot は実際の開発環境を認識できるようになります。

> 📚 **公式ドキュメント**：[About MCP](https://docs.github.com/copilot/concepts/context/mcp) では、MCP が GitHub Copilot とどのように連携するかをより詳しく解説しています。

---

# MCP Server の設定

<img src="images/configuring-mcp-servers.png" alt="Hands adjusting knobs and sliders on a professional audio mixing board representing MCP server configuration" width="800"/>

MCP の動作を確認したところで、追加の server を設定しましょう。このセクションでは、設定ファイルの形式と、新しい server を追加する方法を説明します。

---

## MCP 設定ファイル

MCP server は `~/.copilot/mcp-config.json`（ユーザーレベル、すべてのプロジェクトに適用）または `.vscode/mcp.json`（プロジェクトレベル、現在のワークスペースのみに適用）で設定します。

```json
{
  "mcpServers": {
    "server-name": {
      "type": "local",
      "command": "npx",
      "args": ["@package/server-name"],
      "tools": ["*"]
    }
  }
}
```

*ほとんどの MCP server は npm パッケージとして配布されており、`npx` コマンドで実行します。*

<details>
<summary>💡 <strong>JSON が初めての方は？</strong> 各フィールドの意味を確認するにはこちらをクリック</summary>

| フィールド | 意味 |
|-------|---------------|
| `"mcpServers"` | すべての MCP server 設定のコンテナ |
| `"server-name"` | 任意の名前（例：「github」、「filesystem」） |
| `"type": "local"` | server がローカルマシン上で動作する |
| `"command": "npx"` | 実行するプログラム（npx は npm パッケージを実行する） |
| `"args": [...]` | コマンドに渡す引数 |
| `"tools": ["*"]` | この server のすべてのツールを許可する |

**JSON の重要なルール：**
- 文字列にはダブルクォート `"` を使用する（シングルクォートは不可）
- 最後の項目の後にカンマを付けない
- ファイルは有効な JSON である必要がある（不安な場合は [JSON バリデーター](https://jsonlint.com/) を使用）

</details>

---

## MCP Server の追加

GitHub MCP server は組み込みであり、設定は不要です。以下は追加できる server の一覧です。**興味のあるものを選ぶか、順番に進めてください。**

| やりたいこと | ジャンプ先 |
|---|---|
| Copilot にプロジェクトファイルを参照させる | [Filesystem Server](#filesystem-server) |
| 最新のライブラリドキュメントを取得する | [Context7 Server](#context7-server-documentation) |
| 追加のオプション（カスタム server、web_fetch）を試す | [Beyond the Basics](#beyond-the-basics) |

<details>
<summary><strong>Filesystem Server</strong> - Copilot にプロジェクトファイルを探索させる</summary>
<a id="filesystem-server"></a>

### Filesystem Server

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
      "tools": ["*"]
    }
  }
}
```

> 💡 **`.` パスについて**：`.` は「カレントディレクトリ」を意味します。Copilot は起動した場所を基準に相対的なファイルにアクセスできます。Codespace では、ワークスペースのルートになります。`/workspaces/copilot-cli-for-beginners` のような絶対パスを使用することもできます。

これを `~/.copilot/mcp-config.json` に追加して、Copilot を再起動してください。

</details>

<details>
<summary><strong>Context7 Server</strong> - 最新のライブラリドキュメントを取得する</summary>
<a id="context7-server-documentation"></a>

### Context7 Server（ドキュメント）

Context7 を使うと、Copilot が人気のあるフレームワークやライブラリの最新ドキュメントにアクセスできます。古くなっている可能性のあるトレーニングデータに頼る代わりに、Copilot が実際の最新ドキュメントを取得します。

```json
{
  "mcpServers": {
    "context7": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "tools": ["*"]
    }
  }
}
```

- ✅ **API キー不要**
- ✅ **アカウント不要**
- ✅ **コードはローカルに留まる**

これを `~/.copilot/mcp-config.json` に追加して、Copilot を再起動してください。

</details>

<details>
<summary><strong>Beyond the Basics</strong> - カスタム server と Web アクセス（任意）</summary>
<a id="beyond-the-basics"></a>

上記のコアとなる server に慣れてきたら、こちらの追加オプションをお試しください。

### Microsoft Learn MCP Server

これまで紹介してきた MCP server（filesystem、Context7）はいずれもローカルマシンで動作します。しかし、MCP server はリモートで動作させることもできます。つまり、Copilot CLI に URL を指定するだけで、残りはすべて自動的に処理されます。`npx` や `python` は不要で、ローカルプロセスもなく、依存関係のインストールも必要ありません。

[Microsoft Learn MCP Server](https://github.com/microsoftdocs/mcp) はその良い例です。Copilot CLI に Microsoft の公式ドキュメント（Azure、Microsoft Foundry をはじめとする AI 関連トピック、.NET、Microsoft 365 など）への直接アクセスを与えることで、モデルのトレーニングデータに頼ることなく、ドキュメントの検索、ページの全文取得、公式コードサンプルの発見ができます。

- ✅ **API キー不要**
- ✅ **アカウント不要**
- ✅ **ローカルへのインストール不要**

**`/plugin install` を使ったクイックインストール：**

JSON 設定ファイルを手動で編集する代わりに、コマンド一発でインストールできます。

```bash
copilot

> /plugin install microsoftdocs/mcp
```

これにより、server と関連する agent スキルが自動的に追加されます。インストールされるスキルは以下のとおりです。

- **microsoft-docs**：概念説明、チュートリアル、ファクト確認
- **microsoft-code-reference**：API の調査、コードサンプル、トラブルシューティング
- **microsoft-skill-creator**：Microsoft テクノロジーに関するカスタムスキルを生成するメタスキル

**使い方：**
```bash
copilot

> Python アプリを Azure App Service にデプロイする推奨方法は何ですか？Microsoft Learn で検索してください。
```

📚 詳細：[Microsoft Learn MCP Server の概要](https://learn.microsoft.com/training/support/mcp-get-started)

### `web_fetch` による Web アクセス

Copilot CLI には、任意の URL からコンテンツを取得できる組み込みの `web_fetch` ツールが含まれています。ターミナルを離れることなく、README、API ドキュメント、リリースノートなどを取得するのに便利です。MCP server は不要です。

アクセス可能な URL は `~/.copilot/config.json`（Copilot の全般設定）で制御できます。これは `~/.copilot/mcp-config.json`（MCP server の定義）とは別ファイルです。

```json
{
  "permissions": {
    "allowedUrls": [
      "https://api.github.com/**",
      "https://docs.github.com/**",
      "https://*.npmjs.org/**"
    ],
    "blockedUrls": [
      "http://**"
    ]
  }
}
```

**使い方：**
```bash
copilot

> https://github.com/facebook/react の README を取得して要約してください
```

### カスタム MCP Server の構築

自社の API、データベース、社内ツールに Copilot を接続したい場合は、Python でカスタム MCP server を構築できます。既製の server（GitHub、filesystem、Context7）でほとんどのユースケースは網羅されているため、これは完全に任意です。

📖 book app を例にした完全なウォークスルーは [カスタム MCP Server ガイド](mcp-custom-server.md) をご覧ください。

📚 より詳しい背景については、[MCP for Beginners コース](https://github.com/microsoft/mcp-for-beginners) をご覧ください。

</details>

<a id="complete-configuration-file"></a>

### 完全な設定ファイル

filesystem と Context7 server を含む `mcp-config.json` の全体例を示します。

> 💡 **メモ：** GitHub MCP は組み込みです。設定ファイルに追加する必要はありません。

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
      "tools": ["*"]
    },
    "context7": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "tools": ["*"]
    }
  }
}
```

グローバルに利用する場合は `~/.copilot/mcp-config.json` として保存し、プロジェクト固有の設定には `.vscode/mcp.json` として保存してください。

---

# MCP Server の活用

MCP server を設定したところで、実際に何ができるかを見ていきましょう。

<img src="images/using-mcp-servers.png" alt="Using MCP Servers - Hub-and-spoke diagram showing a Developer CLI connected to GitHub, Filesystem, Context7, and Custom/Web Fetch servers" width="800" />

---

## Server の使用例

**気になる server を選ぶか、順番に進めてください。**

| 試したいこと | ジャンプ先 |
|---|---|
| GitHub のリポジトリ、イシュー、PR | [GitHub Server](#github-server-built-in) |
| プロジェクトファイルの参照 | [Filesystem Server の使い方](#filesystem-server-usage) |
| ライブラリドキュメントの検索 | [Context7 Server の使い方](#context7-server-usage) |
| カスタム server、Microsoft Learn MCP、web_fetch の使い方 | [Beyond the Basics の使い方](#beyond-the-basics-usage) |

<details>
<summary><strong>GitHub Server（組み込み）</strong> - リポジトリ、イシュー、PR などにアクセスする</summary>
<a id="github-server-built-in"></a>

### GitHub Server（組み込み）

GitHub MCP server は**組み込み**です。Copilot にログインしていれば（初期設定時に行ったはずです）、すでに使えます。設定は不要です！

> 💡 **動作しない場合は？** `/login` を実行して GitHub で再認証してください。

<details>
<summary><strong>Dev Container での認証</strong></summary>

- **GitHub Codespaces**（推奨）：認証は自動です。`gh` CLI が Codespace のトークンを引き継ぎます。操作は不要です。
- **ローカル dev container（Docker）**：コンテナが起動したら `gh auth login` を実行し、その後 Copilot を再起動してください。

**認証のトラブルシューティング：**
```bash
# 認証状態の確認
gh auth status

# 未認証の場合はログイン
gh auth login

# GitHub MCP の接続確認
copilot
> /mcp show
```

</details>

| 機能 | 例 |
|---------|----------|
| **リポジトリ情報** | コミット、ブランチ、コントリビューターの確認 |
| **イシュー** | イシューの一覧表示、作成、検索、コメント |
| **Pull Request** | PR の確認、差分の表示、PR の作成、ステータス確認 |
| **コード検索** | リポジトリ全体でのコード検索 |
| **Actions** | ワークフローの実行状況とステータスの照会 |

```bash
copilot

# このリポジトリの最近のアクティビティを確認する
> このリポジトリの最新5件のコミットを一覧表示してください

Recent commits:
1. abc1234 - Update chapter 05 skills examples (2 days ago)
2. def5678 - Add book app test fixtures (3 days ago)
3. ghi9012 - Fix typo in chapter 03 README (4 days ago)
...

# リポジトリの構造を探索する
> このリポジトリにはどのブランチがありますか？

Branches:
- main (default)
- chapter6 (current)

# リポジトリ全体でコードパターンを検索する
> pytest をインポートしているファイルをこのリポジトリで検索してください

Found 1 file:
- samples/book-app-project/tests/test_books.py
```

> 💡 **自分のフォークで作業している場合は？** このコースのリポジトリをフォークしていれば、イシューや pull request の作成などの書き込み操作も試せます。以下の演習で練習します。

> ⚠️ **結果が表示されない場合は？** GitHub MCP はリポジトリのリモート（github.com 上）に対して動作します。ローカルファイルだけではありません。リポジトリにリモートが設定されているか `git remote -v` で確認してください。

</details>

<details>
<summary><strong>Filesystem Server</strong> - プロジェクトファイルの参照と分析</summary>
<a id="filesystem-server-usage"></a>

### Filesystem Server

設定が完了すると、filesystem MCP は Copilot が自動的に使えるツールを提供します。

```bash
copilot

> book-app-project ディレクトリには Python ファイルがいくつありますか？

Found 3 Python files in samples/book-app-project/:
- book_app.py
- books.py
- utils.py

> data.json ファイルの合計サイズはどのくらいですか？

samples/book-app-project/data.json: 2.4 KB

> book app で型ヒントのない関数をすべて見つけてください

Found 2 functions without type hints:
- samples/book-app-project/utils.py:10 - get_user_choice()
- samples/book-app-project/utils.py:14 - get_book_details()
```

</details>

<details>
<summary><strong>Context7 Server</strong> - ライブラリドキュメントを検索する</summary>
<a id="context7-server-usage"></a>

### Context7 Server

```bash
copilot

> pytest フィクスチャを使用するためのベストプラクティスは何ですか？

From pytest Documentation:

Fixtures - Use fixtures to provide a fixed baseline for tests:

    import pytest

    @pytest.fixture
    def sample_books():
        return [
            {"title": "1984", "author": "George Orwell", "year": 1949},
            {"title": "Dune", "author": "Frank Herbert", "year": 1965},
        ]

    def test_find_by_author(sample_books):
        # fixture is automatically passed as argument
        results = [b for b in sample_books if "Orwell" in b["author"]]
        assert len(results) == 1

Best practices:
- Use fixtures instead of setup/teardown methods
- Use tmp_path fixture for temporary files
- Use monkeypatch for modifying environment
- Scope fixtures appropriately (function, class, module, session)

> これを book app のテストファイルに適用するにはどうすればよいですか？

# Copilot now knows the official pytest patterns
# and can apply them to samples/book-app-project/tests/test_books.py
```

</details>

<details>
<summary><strong>Beyond the Basics</strong> - カスタム server と web_fetch の使い方</summary>
<a id="beyond-the-basics-usage"></a>

### Beyond the Basics

**カスタム MCP Server**：[カスタム MCP Server ガイド](mcp-custom-server.md) に従って book-lookup server を構築した場合、蔵書コレクションに直接クエリを実行できます。

```bash
copilot

> book lookup サーバーを使って「1984」の情報を調べてください。George Orwell の本を検索してください
```

**Microsoft Learn MCP**：[Microsoft Learn MCP server](#microsoft-learn-mcp-server) をインストールした場合、Microsoft の公式ドキュメントを直接検索できます。

```bash
copilot

> Azure Function のマネージド ID を設定するにはどうすればよいですか？Microsoft Learn で検索してください。
```

**Web Fetch**：組み込みの `web_fetch` ツールを使うと、任意の URL からコンテンツを取得できます。

```bash
copilot

> https://github.com/facebook/react の README を取得して要約してください
```

</details>

---

## マルチ Server ワークフロー

これらのワークフローは、開発者が「もうこれなしでは働けない」と言う理由を示しています。各例では、1 つのセッションで複数の MCP server を組み合わせて使います。

<img src="images/issue-to-pr-workflow.png" alt="Issue to PR Workflow using MCP - Shows the complete flow from getting a GitHub issue through creating a pull request" width="800"/>

*完全な MCP ワークフロー：GitHub MCP がリポジトリデータを取得し、Filesystem MCP がコードを探索し、Context7 MCP がベストプラクティスを提供し、Copilot が分析を担当します*

以下の各例は独立しています。**興味のあるものを選ぶか、すべて読んでください。**

| 見たい内容 | ジャンプ先 |
|---|---|
| 複数 server の連携 | [マルチ Server 探索](#multi-server-exploration) |
| 1 つのセッションでイシューから PR へ | [Issue-to-PR ワークフロー](#issue-to-pr-workflow) |
| プロジェクトのヘルスチェック | [ヘルスダッシュボード](#health-dashboard) |

<details>
<summary><strong>マルチ Server 探索</strong> - 1 つのセッションで filesystem、GitHub、Context7 を組み合わせる</summary>
<a id="multi-server-exploration"></a>

#### 複数の MCP Server を使った book app の探索

```bash
copilot

# Step 1: filesystem MCP で book app を探索する
> samples/book-app-project/ にあるすべての Python ファイルを一覧表示し、
> 各ファイルの内容を要約してください

Found 3 Python files:
- book_app.py: CLI entry point with command routing (list, add, remove, find)
- books.py: BookCollection class with data persistence via JSON
- utils.py: Helper functions for user input and display

# Step 2: GitHub MCP で最近の変更を確認する
> samples/book-app-project/ のファイルに触れた最新3件のコミットは何ですか？

Recent commits affecting book app:
1. abc1234 - Add test fixtures for BookCollection (2 days ago)
2. def5678 - Add find_by_author method (5 days ago)
3. ghi9012 - Initial book app setup (1 week ago)

# Step 3: Context7 MCP でベストプラクティスを調べる
> JSON データ永続化に関する Python のベストプラクティスは何ですか？

From Python Documentation:
- Use context managers (with statements) for file I/O
- Handle JSONDecodeError for corrupted files
- Use dataclasses for structured data
- Consider atomic writes to prevent data corruption

# Step 4: 提案をまとめる
> book app のコードとこれらのベストプラクティスをもとに、
> どのような改善を提案しますか？

Suggestions:
1. Add input validation in add_book() for empty strings and invalid years
2. Consider atomic writes in save_books() to prevent data corruption
3. Add type hints to utils.py functions (get_user_choice, get_book_details)
```

<details>
<summary>🎬 MCP ワークフローの実際の動作を確認する！</summary>

![MCP Workflow Demo](images/mcp-workflow-demo.gif)

*デモの出力は異なる場合があります。使用するモデル、ツール、レスポンスはここで示されたものと異なります。*

</details>

**結果**：コードの探索 → 履歴の確認 → ベストプラクティスの参照 → 改善計画。**すべて 1 つのターミナルセッションで、3 つの MCP server を組み合わせて実現しています。**

</details>

<details>
<summary><strong>Issue-to-PR ワークフロー</strong> - ターミナルを離れずに GitHub イシューから pull request へ</summary>
<a id="issue-to-pr-workflow"></a>

#### Issue-to-PR ワークフロー（自分のリポジトリで）

これは書き込み権限を持つ自分のフォークまたはリポジトリで最もよく機能します。

> 💡 **今すぐ試せなくても大丈夫です。** 読み取り専用のクローンの場合は、課題で練習します。今は流れを理解するために読み進めてください。

```bash
copilot

> GitHub のイシュー #1 の詳細を教えてください

Issue #1: Add input validation for book year
Status: Open
Description: The add_book function accepts any year value...

> @samples/book-app-project/books.py Fix the issue described in issue #1

[Copilot implements year validation in add_book()]

> テストを実行して修正が正しく動作することを確認してください

All 8 tests passed ✓

> 「Add year validation to book app」というタイトルで pull request を作成してください

✓ Created PR #2: Add year validation to book app
```

**コピーペーストなし。コンテキストの切り替えなし。1 つのターミナルセッションで完結。**

</details>

<details>
<summary><strong>ヘルスダッシュボード</strong> - 複数 server を使ってプロジェクトのヘルスチェックを素早く実施する</summary>
<a id="health-dashboard"></a>

#### Book App ヘルスダッシュボード

```bash
copilot

> book app プロジェクトのヘルスレポートを作成してください：
> 1. samples/book-app-project/ の Python ファイル全体にわたるすべての関数を一覧表示する
> 2. 型ヒントがある関数とない関数を確認する
> 3. samples/book-app-project/tests/ に存在するテストを表示する
> 4. このディレクトリの最近のコミット履歴を確認する

Book App Health Report
======================

📊 Functions Found:
- books.py: 8 methods in BookCollection (all have type hints ✓)
- book_app.py: 6 functions (4 have type hints, 2 missing)
- utils.py: 3 functions (1 has type hints, 2 missing)

🧪 Test Coverage:
- test_books.py: 8 test functions covering BookCollection
- Missing: no tests for book_app.py CLI functions
- Missing: no tests for utils.py helper functions

📝 Recent Activity:
- 3 commits in the last week
- Most recent: added test fixtures

Recommendations:
- Add type hints to utils.py functions
- Add tests for book_app.py CLI handlers
- All files well-sized (<100 lines) - good structure!
```

**結果**：複数のデータソースを数秒で集約。手作業なら、grep を実行し、行数を数え、git log を確認し、テストファイルを参照する必要があります。軽く 15 分以上かかる作業です。

</details>

---

# 演習

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

**🎉 基本はすべて習得しました！** MCP を理解し、server の設定方法を学び、実際のワークフローを確認しました。次は自分で試してみましょう。

---

## ▶️ やってみよう

さあ、あなたの番です！以下の演習で、book app プロジェクトを使いながら MCP server の練習をしましょう。

### 演習 1：MCP のステータスを確認する

まず、利用可能な MCP server を確認します。

```bash
copilot

> /mcp show
```

GitHub server が有効として表示されるはずです。表示されない場合は `/login` を実行して認証してください。

---

### 演習 2：Filesystem MCP で book app を探索する

filesystem server を設定済みの場合は、book app の探索に使ってみましょう。

```bash
copilot

> samples/book-app-project/ には Python ファイルがいくつありますか？
> 各ファイルにはどのような関数が定義されていますか？
```

**期待される結果**：Copilot が `book_app.py`、`books.py`、`utils.py` とそれぞれの関数を一覧表示します。

> 💡 **filesystem MCP をまだ設定していない場合は？** 上記の[完全な設定ファイル](#complete-configuration-file)セクションの設定ファイルを作成してください。その後、Copilot を再起動します。

---

### 演習 3：GitHub MCP でリポジトリの履歴を照会する

組み込みの GitHub MCP を使って、このコースのリポジトリを探索します。

```bash
copilot

> このリポジトリの最新5件のコミットを一覧表示してください

> このリポジトリにはどのブランチがありますか？
```

**期待される結果**：Copilot が GitHub リモートから最近のコミットメッセージとブランチ名を表示します。

> ⚠️ **Codespace の場合は？** 自動的に動作します。認証が引き継がれます。ローカルクローンの場合は、`gh auth status` でログイン済みであることを確認してください。

---

### 演習 4：複数の MCP Server を組み合わせる

1 つのセッションで filesystem と GitHub MCP を組み合わせてみましょう。

```bash
copilot

> samples/book-app-project/data.json を読んで、コレクションにどのような本があるか教えてください。
> その後、最近のコミットを確認して、このファイルが最後に更新された時期を調べてください。
```

**期待される結果**：Copilot が JSON ファイルを読み（filesystem MCP）、「The Hobbit」「1984」「Dune」「To Kill a Mockingbird」「Mysterious Book」の 5 冊を含む蔵書を一覧表示し、GitHub のコミット履歴を照会します。

**自己チェック**：「リポジトリのコミット履歴を確認する」が、手動で `git log` を実行してプロンプトに貼り付けるよりも優れている理由を説明できれば、MCP を理解しています。

---

## 📝 課題

### メインチャレンジ：Book App MCP 探索

book app プロジェクトで MCP server を組み合わせて使う練習をしましょう。1 つの Copilot セッションで以下のステップを完了してください。

1. **MCP が動作していることを確認する**：`/mcp show` を実行し、少なくとも GitHub server が有効になっていることを確認する
2. **filesystem MCP を設定する**（まだの場合）：filesystem server の設定で `~/.copilot/mcp-config.json` を作成する
3. **コードを探索する**：Copilot に filesystem server を使って以下を行うよう指示する
   - `samples/book-app-project/books.py` のすべての関数を一覧表示する
   - `samples/book-app-project/utils.py` のどの関数に型ヒントがないかを確認する
   - `samples/book-app-project/data.json` を読んでデータ品質の問題を特定する（ヒント：最後のエントリを確認）
4. **リポジトリのアクティビティを確認する**：Copilot に GitHub MCP を使って以下を行うよう指示する
   - `samples/book-app-project/` のファイルに触れた最近のコミットを一覧表示する
   - オープンなイシューや pull request がないか確認する
5. **server を組み合わせる**：1 つのプロンプトで Copilot に以下を行うよう指示する
   - `samples/book-app-project/tests/test_books.py` のテストファイルを読む
   - テスト済みの関数を `books.py` のすべての関数と比較する
   - 不足しているテストカバレッジをまとめる

**成功基準**：1 つの Copilot セッションで filesystem と GitHub MCP のデータをシームレスに組み合わせられること、および各 MCP server がレスポンスに何を提供したかを説明できること。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**ステップ 1：MCP の確認**
```bash
copilot
> /mcp show
# 「github」が enabled と表示されるはず
# 表示されない場合は：/login を実行
```

**ステップ 2：設定ファイルの作成**

上記の[完全な設定ファイル](#complete-configuration-file)セクションの JSON を使用して `~/.copilot/mcp-config.json` として保存してください。

**ステップ 3：確認すべきデータ品質の問題**

`data.json` の最後の書籍はこちらです。
```json
{
  "title": "Mysterious Book",
  "author": "",
  "year": 0,
  "read": false
}
```
著者が空欄で年が 0 になっています。これがデータ品質の問題です！

**ステップ 5：テストカバレッジの比較**

`test_books.py` のテストがカバーしている関数：`add_book`、`mark_as_read`、`remove_book`、`get_unread_books`、`find_book_by_title`。`load_books`、`save_books`、`list_books` などの関数には直接のテストがありません。`book_app.py` の CLI 関数と `utils.py` のヘルパーにはテストがまったくありません。

**MCP が動作しない場合：** 設定ファイルを編集した後、Copilot を再起動してください。

</details>

### ボーナスチャレンジ：カスタム MCP Server を構築する

さらに深く学びたい場合は、[カスタム MCP Server ガイド](mcp-custom-server.md) に従って、任意の API に接続するカスタム MCP server を Python で構築してみましょう。

---

<details>
<summary>🔧 <strong>よくある間違いとトラブルシューティング</strong>（クリックして展開）</summary>

### よくある間違い

| 間違い | 何が起きるか | 対処法 |
|---------|--------------|-----|
| GitHub MCP が組み込みであることを知らない | 手動でインストール・設定しようとする | GitHub MCP はデフォルトで含まれています。「List the recent commits in this repo」と試してみてください |
| 設定ファイルの場所を間違える | MCP の設定を見つけられない、編集できない | ユーザーレベルの設定は `~/.copilot/mcp-config.json`、プロジェクトレベルは `.vscode/mcp.json` |
| 設定ファイルの JSON が無効 | MCP server が読み込まれない | `/mcp show` で設定を確認し、JSON の構文を検証する |
| MCP server の認証を忘れる | 「Authentication failed」エラー | 一部の MCP は個別の認証が必要です。各 server の要件を確認してください |

### トラブルシューティング

**「MCP server not found」** - 以下を確認してください。
1. npm パッケージが存在するか：`npm view @modelcontextprotocol/server-github`
2. 設定が有効な JSON であるか
3. server 名が設定と一致しているか

`/mcp show` を使って現在の設定を確認してください。

**「GitHub authentication failed」** - 組み込みの GitHub MCP は `/login` の認証情報を使用します。次を試してください。

```bash
copilot
> /login
```

これにより GitHub で再認証されます。問題が続く場合は、GitHub アカウントがアクセスしようとしているリポジトリに必要な権限を持っているか確認してください。

**「MCP server failed to start」** - server のログを確認してください。
```bash
# server コマンドを手動で実行してエラーを確認する
npx -y @modelcontextprotocol/server-github
```

**MCP ツールが使用できない** - server が有効になっているか確認してください。
```bash
copilot

> /mcp show
# server が一覧に表示されていて有効かどうかを確認
```

server が無効になっている場合は、以下の[追加の `/mcp` コマンド](#-additional-mcp-commands)で再有効化する方法を確認してください。

</details>

---

<details>
<summary>📚 <strong>追加の <code>/mcp</code> コマンド</strong>（クリックして展開）</summary>
<a id="-additional-mcp-commands"></a>

`/mcp show` 以外にも、MCP server を管理するためのコマンドがいくつかあります。

| コマンド | 機能 |
|---------|--------------|
| `/mcp show` | 設定済みのすべての MCP server とそのステータスを表示する |
| `/mcp add` | 新しい server を追加するためのインタラクティブな設定 |
| `/mcp edit <server-name>` | 既存の server 設定を編集する |
| `/mcp enable <server-name>` | 無効な server を有効にする |
| `/mcp disable <server-name>` | server を一時的に無効にする |
| `/mcp delete <server-name>` | server を完全に削除する |

このコースのほとんどの場面では `/mcp show` だけで十分です。その他のコマンドは、複数の server を管理するようになってから役立ちます。

</details>

---

# まとめ

## 🔑 重要なポイント

1. **MCP** は Copilot を外部サービス（GitHub、ファイルシステム、ドキュメント）に接続します
2. **GitHub MCP は組み込み** - 設定不要で `/login` するだけで使えます
3. **Filesystem と Context7** は `~/.copilot/mcp-config.json` で設定します
4. **マルチ server ワークフロー** は 1 つのセッションで複数のソースからデータを組み合わせます
5. **server のステータス確認** は `/mcp show` で行います（server 管理には追加コマンドも利用可能）
6. **カスタム server** を使えば任意の API に接続できます（任意、付録のガイドで解説）

> 📋 **クイックリファレンス**：コマンドとショートカットの完全なリストは [GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference) をご覧ください。

---

## ➡️ 次のステップ

これですべての構成要素が揃いました：モード、コンテキスト、ワークフロー、agent、スキル、そして MCP。いよいよすべてを組み合わせる時です。

**[Chapter 07: Putting It All Together](../07-putting-it-together/README.md)** では、以下を学びます。

- agent、スキル、MCP を統合したワークフローへの組み合わせ方
- アイデアからマージ済み PR までの完全な機能開発
- フックを使った自動化
- チーム環境でのベストプラクティス

---

**[← Chapter 05 に戻る](../05-skills/README.md)** | **[Chapter 07 へ進む →](../07-putting-it-together/README.md)**
