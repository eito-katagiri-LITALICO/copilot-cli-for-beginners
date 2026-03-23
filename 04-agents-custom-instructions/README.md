![Chapter 04: Agents and Custom Instructions](images/chapter-header.png)

> **Python のコードレビュアー、テスト専門家、セキュリティレビュアーを... 1 つのツールにまとめて雇えるとしたら？**

Chapter 03 では、コードレビュー、リファクタリング、デバッグ、テスト生成、git 連携といった基本的なワークフローをマスターしました。これらにより、GitHub Copilot CLI で高い生産性を発揮できるようになりました。それをさらに発展させましょう。

これまで Copilot CLI を汎用アシスタントとして使ってきました。Agent を使うと、型ヒントと PEP 8 を強制するコードレビュアーや pytest ケースを書くテストヘルパーなど、組み込みの標準を持つ特定のペルソナを与えることができます。同じプロンプトでも、的を絞った instructions を持つ agent が処理すると、明らかに優れた結果が得られることを確認できます。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- 組み込み agent の使用：Plan (`/plan`)、Code-review (`/review`)、および自動 agent（Explore、Task）の理解
- agent ファイル（`.agent.md`）を使った専門化された agent の作成
- ドメイン固有のタスクへの agent の活用
- `/agent` と `--agent` を使った agent の切り替え
- プロジェクト固有の標準に合わせたカスタム instruction ファイルの作成

> ⏱️ **推定所要時間**：約 55 分（読書 20 分 + ハンズオン 35 分）

---

## 🧩 現実世界のアナロジー：専門家を雇う

家のことで助けが必要なとき、"何でも屋" を 1 人呼ぶわけではありません。専門家を呼びます：

| 問題 | 専門家 | 理由 |
|---------|------------|-----|
| 水漏れ | 配管工 | 配管規則に詳しく、専門道具を持っている |
| 電気配線 | 電気工事士 | 安全要件を理解し、規格に準拠している |
| 屋根の交換 | 屋根職人 | 材料に詳しく、地域の気候を考慮できる |

Agent も同じ仕組みで動作します。汎用 AI の代わりに、特定のタスクに集中し、適切なプロセスを知っている agent を使用します。instructions を一度設定すれば、コードレビュー、テスト、セキュリティ、ドキュメント作成など、その専門知識が必要なときにいつでも再利用できます。

<img src="images/hiring-specialists-analogy.png" alt="Hiring Specialists Analogy - Just as you call specialized tradespeople for house repairs, AI agents are specialized for specific tasks like code review, testing, security, and documentation" width="800" />

---

# Agent の使い方

組み込み agent とカスタム agent をすぐに使い始めましょう。

---

## *Agent が初めてですか？* まずここから！
Agent を使ったことも作ったこともない方へ。このコースを始めるために必要なことをすべてお伝えします。

1. **今すぐ *組み込み* agent を試してみましょう：**
   ```bash
   copilot
   > /plan book app の本の発行年に入力バリデーションを追加する
   ```
   これにより Plan agent が呼び出され、ステップバイステップの実装計画が作成されます。

2. **カスタム agent の例を確認しましょう：** agent の instructions を定義するのは簡単です。用意されている [python-reviewer.agent.md](../.github/agents/python-reviewer.agent.md) ファイルでパターンを確認してください。

3. **コアコンセプトを理解しましょう：** Agent はジェネラリストではなく専門家に相談するようなものです。"frontend agent" はアクセシビリティやコンポーネントパターンに自動的に集中します。それを毎回伝える必要はありません。agent の instructions にすでに指定されているからです。


## 組み込み Agent

**Chapter 03 の Development Workflow で組み込み agent をすでに使用しています！**
<br>`/plan` と `/review` は実際には組み込み agent です。裏側で何が起きているかがわかりましたね。完全なリストはこちらです：

| Agent | 呼び出し方 | 機能 |
|-------|---------------|--------------|
| **Plan** | `/plan` または `Shift+Tab`（モード切り替え）| コーディング前にステップバイステップの実装計画を作成する |
| **Code-review** | `/review` | ステージ済み・未ステージの変更を、的を絞った実用的なフィードバックとともにレビューする |
| **Init** | `/init` | プロジェクト設定ファイル（instructions、agents）を生成する |
| **Explore** | *自動* | コードベースの探索や分析を Copilot に依頼したときに内部で使用される |
| **Task** | *自動* | テスト、ビルド、lint、依存関係のインストールなどのコマンドを実行する |

<br>

**組み込み agent の実演** - Plan、Code-review、Explore、Task を呼び出す例

```bash
copilot

# Invoke the Plan agent to create an implementation plan
> /plan book app の本の発行年に入力バリデーションを追加する

# Invoke the Code-review agent on your changes
> /review

# Explore and Task agents are invoked automatically when relevant:
> テストスイートを実行してください        # Task agent を使用

> 本のデータがどのように読み込まれるか調べてください    # Explore agent を使用
```

Task Agent についてはどうでしょうか？Task Agent は裏側で動作し、進行状況を管理・追跡して、わかりやすいフォーマットで報告します：

| 結果 | 表示内容 |
|---------|--------------|
| ✅ **成功** | 簡潔なサマリー（例：「All 247 tests passed」、「Build succeeded」） |
| ❌ **失敗** | スタックトレース、コンパイルエラー、詳細ログを含む完全な出力 |


> 📚 **公式ドキュメント**：[GitHub Copilot CLI Agents](https://docs.github.com/copilot/how-tos/use-copilot-agents/use-copilot-cli#use-custom-agents)

---

# Copilot CLI への Agent の追加

自分の agent をワークフローの一部として定義するだけです！一度定義すれば、後は指示するだけです！

<img src="images/using-agents.png" alt="Four colorful AI robots standing together, each with different tools representing specialized agent capabilities" width="800"/>

## 🗂️ Agent を追加する

Agent ファイルは `.agent.md` 拡張子を持つ markdown ファイルです。YAML frontmatter（メタデータ）と markdown の instructions の 2 つのパートで構成されています。

> 💡 **YAML frontmatter が初めてですか？** ファイルの先頭に `---` マーカーで囲まれた設定のブロックです。YAML は `key: value` のペアで構成されています。残りのファイルは通常の markdown です。

最小限の agent の例：

```markdown
---
name: my-reviewer
description: Code reviewer focused on bugs and security issues
---

# Code Reviewer

You are a code reviewer focused on finding bugs and security issues.

When reviewing code, always check for:
- SQL injection vulnerabilities
- Missing error handling
- Hardcoded secrets
```

> 💡 **必須 vs 任意**：`description` フィールドは必須です。`name`、`tools`、`model` などの他のフィールドは任意です。

## Agent ファイルの配置場所

| 配置場所 | スコープ | 用途 |
|----------|-------|----------|
| `.github/agents/` | プロジェクト固有 | プロジェクトの規約を共有するチーム用 agent |
| `~/.copilot/agents/` | グローバル（すべてのプロジェクト）| どのプロジェクトでも使う個人用 agent |

**このプロジェクトには [.github/agents/](../.github/agents/) フォルダーにサンプル agent ファイルが含まれています**。自分で作成することも、すでに提供されているものをカスタマイズすることもできます。

<details>
<summary>📂 このコースのサンプル agent を確認する</summary>

| ファイル | 説明 |
|------|-------------|
| `hello-world.agent.md` | 最小限の例 - まずここから |
| `python-reviewer.agent.md` | Python コード品質レビュアー |
| `pytest-helper.agent.md` | pytest テスト専門家 |

```bash
# Or copy one to your personal agents folder (available in every project)
cp .github/agents/python-reviewer.agent.md ~/.copilot/agents/
```

コミュニティの agent をさらに確認するには、[github/awesome-copilot](https://github.com/github/awesome-copilot) を参照してください。

</details>


## 🚀 カスタム agent を使用する 2 つの方法

### インタラクティブモード
インタラクティブモード内で `/agent` を使って agent を一覧表示し、作業に使用する agent を選択します。
会話を続ける agent を選択してください。

```bash
copilot
> /agent
```

別の agent に切り替えるか、デフォルトモードに戻るには、再度 `/agent` コマンドを使用してください。

### プログラマティックモード

agent を指定して直接新しいセッションを開始します。

```bash
copilot --agent python-reviewer
> @samples/book-app-project/books.py をレビューしてください
```

> 💡 **Agent の切り替え**：`/agent` または `--agent` を再度使用すれば、いつでも別の agent に切り替えられます。標準の Copilot CLI の使用感に戻るには、`/agent` を使用して **no agent** を選択してください。

---

# Agent をより深く理解する

<img src="images/creating-custom-agents.png" alt="Robot being assembled on a workbench surrounded by components and tools representing custom agent creation" width="800"/>

> 💡 **このセクションはオプションです。** 組み込み agent（`/plan`、`/review`）はほとんどのワークフローに対して十分な機能を持っています。作業全体に一貫して適用される専門的な知識が必要な場合に、カスタム agent を作成してください。

以下の各トピックは独立しています。**興味のあるものを選んでください - すべてを一度に読む必要はありません。**

| やりたいこと | 移動先 |
|---|---|
| agent が汎用プロンプトより優れている理由を知る | [Specialist vs Generic](#specialist-vs-generic-see-the-difference) |
| 機能開発で agent を組み合わせる | [Working with Multiple Agents](#working-with-multiple-agents) |
| agent の整理、命名、共有 | [Organizing & Sharing Agents](#organizing--sharing-agents) |
| 常時有効なプロジェクトコンテキストの設定 | [Configuring Your Project for Copilot](#configuring-your-project-for-copilot) |
| YAML プロパティとツールの参照 | [Agent File Reference](#agent-file-reference) |

以下のシナリオを選択して展開してください。

---

<a id="specialist-vs-generic-see-the-difference"></a>
<details>
<summary><strong>Specialist vs Generic: See the Difference</strong> - agent が汎用プロンプトより優れた出力を生成する理由</summary>

## Specialist vs Generic: 違いを確認する

これが agent が価値を証明する場面です。違いをご覧ください：

### Agent なし（汎用 Copilot）

```bash
copilot

> book app に出版年の範囲で本を検索する関数を追加してください
```

**汎用出力**：
```python
def search_by_year_range(books, start_year, end_year):
    results = []
    for book in books:
        if book['year'] >= start_year and book['year'] <= end_year:
            results.append(book)
    return results
```

基本的な実装です。動きはします。しかし、多くが欠けています。

---

### Python Reviewer Agent あり

```bash
copilot

> /agent
# Select "python-reviewer"

> book app に出版年の範囲で本を検索する関数を追加してください
```

**専門家の出力**：
```python
from typing import List, Dict, Any

def search_by_year_range(
    books: List[Dict[str, Any]],
    start_year: int,
    end_year: int
) -> List[Dict[str, Any]]:
    """
    Search for books within a specified year range.

    Args:
        books: List of book dictionaries
        start_year: Starting year (inclusive)
        end_year: Ending year (inclusive)

    Returns:
        List of books published within the year range

    Raises:
        ValueError: If start_year is greater than end_year
        TypeError: If year values are not integers
    """
    if not isinstance(start_year, int) or not isinstance(end_year, int):
        raise TypeError("Year values must be integers")

    if start_year > end_year:
        raise ValueError(f"Start year ({start_year}) cannot be greater than end year ({end_year})")

    return [
        book for book in books
        if isinstance(book.get('year'), int)
        and start_year <= book['year'] <= end_year
    ]
```

**python-reviewer agent が自動的に含めるもの**：
- ✅ すべてのパラメーターと戻り値への型ヒント
- ✅ Args/Returns/Raises を含む包括的な docstring
- ✅ 適切なエラーハンドリングを伴う入力バリデーション
- ✅ より良いパフォーマンスのためのリスト内包表記
- ✅ エッジケースの処理（year の値が欠落・無効な場合）
- ✅ PEP 8 準拠のフォーマット
- ✅ 防衛的プログラミングの実践

**違い**：同じプロンプトで、劇的に優れた出力を得られます。Agent は、あなたが頼み忘れてしまうような専門知識を持ち込みます。

</details>

---

<a id="working-with-multiple-agents"></a>
<details>
<summary><strong>Working with Multiple Agents</strong> - 専門家の組み合わせ、セッション中の切り替え、ツールとしての agent</summary>

## 複数の Agent を使って作業する

真の力は、専門家が 1 つの機能に対して協力して取り組むときに発揮されます。

### 例：シンプルな機能の構築

```bash
copilot

> book app に「出版年範囲で検索」機能を追加したいと思います

# Use python-reviewer for design
> /agent
# Select "python-reviewer"

> @samples/book-app-project/books.py find_by_year_range メソッドを設計してください。最善のアプローチは何ですか？

# Switch to pytest-helper for test design
> /agent
# Select "pytest-helper"

> @samples/book-app-project/tests/test_books.py find_by_year_range メソッドのテストケースを設計してください。
> どのエッジケースをカバーすべきですか？

# Synthesize both designs
> メソッドの実装と包括的なテストを含む実装計画を作成してください。
```

**重要な洞察**：あなたは専門家を指揮するアーキテクトです。細部は彼らが処理し、あなたはビジョンを持ちます。

<details>
<summary>🎬 実際の動作を確認する！</summary>

![Python Reviewer Demo](images/python-reviewer-demo.gif)

*デモの出力はさまざまです。お使いのモデル、ツール、応答は、ここに示されたものと異なる場合があります。*

</details>

### ツールとしての Agent

agent が設定されている場合、Copilot は複雑なタスクの実行中にそれらをツールとして呼び出すこともできます。フルスタックの機能を依頼すると、Copilot は適切な専門 agent に一部を自動的に委任することがあります。

</details>

---

<a id="organizing--sharing-agents"></a>
<details>
<summary><strong>Organizing & Sharing Agents</strong> - 命名、ファイルの配置、instruction ファイル、チームでの共有</summary>

## Agent の整理と共有

### Agent の命名

agent ファイルを作成する際、名前は重要です。`/agent` または `--agent` の後に入力するものであり、チームメンバーが agent リストで確認するものです。

| ✅ 良い名前 | ❌ 避けるべき |
|--------------|----------|
| `frontend` | `my-agent` |
| `backend-api` | `agent1` |
| `security-reviewer` | `helper` |
| `react-specialist` | `code` |
| `python-backend` | `assistant` |

**命名規則：**
- ハイフン付きの小文字を使用する：`my-agent-name.agent.md`
- ドメインを含める：`frontend`、`backend`、`devops`、`security`
- 必要に応じて具体的にする：`react-typescript` vs `frontend` だけ

---

### チームとの共有

agent ファイルを `.github/agents/` に配置すれば、バージョン管理されます。リポジトリにプッシュすれば、すべてのチームメンバーが自動的に取得できます。しかし、agent は Copilot がプロジェクトから読み込むファイルの 1 種類にすぎません。Copilot は **instruction ファイル** もサポートしており、誰かが `/agent` を実行しなくても、すべてのセッションに自動的に適用されます。

このように考えてください：agent は必要なときに呼び出す専門家であり、instruction ファイルは常に有効なチームのルールです。

### ファイルの配置場所

2 つの主要な配置場所はすでにご存じです（上記の [Agent ファイルの配置場所](#where-to-put-agent-files) を参照）。以下のデシジョンツリーを使って選択してください：

<img src="images/agent-file-placement-decision-tree.png" alt="Decision tree for where to put agent files: experimenting → current folder, team use → .github/agents/, everywhere → ~/.copilot/agents/" width="800"/>

**シンプルに始めましょう：** プロジェクトフォルダーに単一の `*.agent.md` ファイルを作成してください。満足したら、恒久的な場所に移動します。

agent ファイル以外にも、Copilot は **プロジェクトレベルの instruction ファイル** を自動的に読み込みます。`/agent` は不要です。`AGENTS.md`、`.instructions.md`、`/init` については、以下の [Copilot 向けのプロジェクト設定](#configuring-your-project-for-copilot) を参照してください。

</details>

---

<a id="configuring-your-project-for-copilot"></a>
<details>
<summary><strong>Configuring Your Project for Copilot</strong> - AGENTS.md、instruction ファイル、/init の設定</summary>

## Copilot 向けのプロジェクト設定

Agent は必要に応じて呼び出す専門家です。**プロジェクト設定ファイル** は異なります：Copilot はすべてのセッションで自動的にそれらを読み込み、プロジェクトの規約、技術スタック、ルールを理解します。`/agent` を実行する必要はありません。リポジトリで作業するすべての人に対して、コンテキストは常に有効です。

### /init を使ったクイックセットアップ

始める最も速い方法は、Copilot に設定ファイルを生成させることです：

```bash
copilot
> /init
```

Copilot はプロジェクトをスキャンし、カスタマイズされた instruction ファイルを作成します。後で編集することができます。

### Instruction ファイルの形式

| ファイル | スコープ | 備考 |
|------|-------|-------|
| `AGENTS.md` | プロジェクトルートまたはネスト | **クロスプラットフォーム標準** - Copilot および他の AI アシスタントで動作する |
| `.github/copilot-instructions.md` | プロジェクト | GitHub Copilot 固有 |
| `.github/instructions/*.instructions.md` | プロジェクト | 粒度の細かいトピック固有の instructions |
| `CLAUDE.md`、`GEMINI.md` | プロジェクトルート | 互換性のためにサポートされている |

> 🎯 **始めたばかりですか？** プロジェクトの instructions には `AGENTS.md` を使用してください。他の形式は必要に応じて後で確認できます。

### AGENTS.md

`AGENTS.md` は推奨される形式です。Copilot と他の AI コーディングツールで機能する [オープンスタンダード](https://agents.md/) です。リポジトリのルートに配置するだけで、Copilot が自動的に読み込みます。このプロジェクト自体の [AGENTS.md](../AGENTS.md) が実際の例です。

一般的な `AGENTS.md` には、プロジェクトのコンテキスト、コードスタイル、セキュリティ要件、テスト標準が記述されています。`/init` を使って生成するか、サンプルファイルのパターンに従って独自に作成してください。

### カスタム Instruction ファイル（.instructions.md）

より細かい制御を求めるチームには、instructions をトピック固有のファイルに分割することをお勧めします。各ファイルは 1 つの関心事を扱い、自動的に適用されます：

```
.github/
└── instructions/
    ├── python-standards.instructions.md
    ├── security-checklist.instructions.md
    └── api-design.instructions.md
```

> 💡 **注意**：Instruction ファイルはどの言語でも使用できます。この例ではコースプロジェクトに合わせて Python を使用していますが、TypeScript、Go、Rust、またはチームが使用する任意の技術向けに同様のファイルを作成できます。

**コミュニティの instruction ファイルを見つける**：.NET、Angular、Azure、Python、Docker などの多くの技術向けに作成済みの instruction ファイルを [github/awesome-copilot](https://github.com/github/awesome-copilot) で探してみましょう。

### カスタム Instructions の無効化

すべてのプロジェクト固有の設定を Copilot に無視させる必要がある場合（デバッグや動作比較に役立ちます）：

```bash
copilot --no-custom-instructions
```

</details>

---

<a id="agent-file-reference"></a>
<details>
<summary><strong>Agent File Reference</strong> - YAML プロパティ、ツールエイリアス、完全な例</summary>

## Agent ファイルリファレンス

### より完全な例

上記の [最小限の agent 形式](#-add-your-agents) を確認しました。次に、`tools` プロパティを使用したより包括的な agent の例を示します。`~/.copilot/agents/python-reviewer.agent.md` を作成してください：

```markdown
---
name: python-reviewer
description: Python code quality specialist for reviewing Python projects
tools: ["read", "edit", "search", "execute"]
---

# Python Code Reviewer

You are a Python specialist focused on code quality and best practices.

**Your focus areas:**
- Code quality (PEP 8, type hints, docstrings)
- Performance optimization (list comprehensions, generators)
- Error handling (proper exception handling)
- Maintainability (DRY principles, clear naming)

**Code style requirements:**
- Use Python 3.10+ features (dataclasses, type hints, pattern matching)
- Follow PEP 8 naming conventions
- Use context managers for file I/O
- All functions must have type hints and docstrings

**When reviewing code, always check:**
- Missing type hints on function signatures
- Mutable default arguments
- Proper error handling (no bare except)
- Input validation completeness
```

### YAML プロパティ

| プロパティ | 必須 | 説明 |
|----------|----------|-------------|
| `name` | いいえ | 表示名（デフォルトはファイル名）|
| `description` | **はい** | agent が何をするか - Copilot がいつ提案するかを理解するのに役立つ |
| `tools` | いいえ | 許可されたツールのリスト（省略 = すべてのツールが使用可能）。以下のツールエイリアスを参照。 |
| `target` | いいえ | `vscode` または `github-copilot` のみに制限する |

### ツールエイリアス

`tools` リストでは以下の名前を使用してください：
- `read` - ファイルの内容を読み込む
- `edit` - ファイルを編集する
- `search` - ファイルを検索する（grep/glob）
- `execute` - シェルコマンドを実行する（`shell`、`Bash` も使用可能）
- `agent` - 他のカスタム agent を呼び出す

> 📖 **公式ドキュメント**：[Custom agents configuration](https://docs.github.com/copilot/reference/custom-agents-configuration)
>
> ⚠️ **VS Code のみ**：`model` プロパティ（AI モデルの選択用）は VS Code では機能しますが、GitHub Copilot CLI ではサポートされていません。クロスプラットフォームの agent ファイルに安全に含めることができます。GitHub Copilot CLI はそれを無視します。

### その他の Agent テンプレート

> 💡 **初心者へのメモ**：以下の例はテンプレートです。**特定の技術はプロジェクトで使用しているものに置き換えてください。** 重要なのは、言及されている特定の技術ではなく、agent の *構造* です。

このプロジェクトには [.github/agents/](../.github/agents/) フォルダーに動作するサンプルが含まれています：
- [hello-world.agent.md](../.github/agents/hello-world.agent.md) - 最小限の例、まずここから
- [python-reviewer.agent.md](../.github/agents/python-reviewer.agent.md) - Python コード品質レビュアー
- [pytest-helper.agent.md](../.github/agents/pytest-helper.agent.md) - pytest テスト専門家

コミュニティの agent については、[github/awesome-copilot](https://github.com/github/awesome-copilot) を参照してください。

</details>

---

# 練習

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

自分の agent を作成して、実際に動かしてみましょう。

---

## ▶️ 実際に試してみましょう

```bash

# Create the agents directory (if it doesn't exist)
mkdir -p .github/agents

# Create a code reviewer agent
cat > .github/agents/reviewer.agent.md << 'EOF'
---
name: reviewer
description: Senior code reviewer focused on security and best practices
---

# Code Reviewer Agent

You are a senior code reviewer focused on code quality.

**Review priorities:**
1. Security vulnerabilities
2. Performance issues
3. Maintainability concerns
4. Best practice violations

**Output format:**
Provide issues as a numbered list with severity tags:
[CRITICAL], [HIGH], [MEDIUM], [LOW]
EOF

# Create a documentation agent
cat > .github/agents/documentor.agent.md << 'EOF'
---
name: documentor
description: Technical writer for clear and complete documentation
---

# Documentation Agent

You are a technical writer who creates clear documentation.

**Documentation standards:**
- Start with a one-sentence summary
- Include usage examples
- Document parameters and return values
- Note any gotchas or limitations
EOF

# Now use them
copilot --agent reviewer
> @samples/book-app-project/books.py をレビューしてください

# Or switch agents
copilot
> /agent
# Select "documentor"
> @samples/book-app-project/books.py をドキュメント化してください
```

---

## 📝 課題

### メインチャレンジ：専門化された Agent チームを構築する

ハンズオン例では `reviewer` と `documentor` agent を作成しました。次は、本のアプリのデータバリデーションを改善するという別のタスクで、agent の作成と使用を練習しましょう：

1. 本のアプリに合わせた 3 つの agent ファイル（`.agent.md`）を作成し、`.github/agents/` に配置します
2. 作成する agent：
   - **data-validator**：`data.json` の欠落したデータや不正なデータ（空の著者、year=0、欠落フィールド）をチェックする
   - **error-handler**：Python コードの一貫性のないエラーハンドリングをレビューし、統一されたアプローチを提案する
   - **doc-writer**：docstring と README のコンテンツを生成または更新する
3. 各 agent を本のアプリで使用する：
   - `data-validator` → `@samples/book-app-project/data.json` の監査
   - `error-handler` → `@samples/book-app-project/books.py` と `@samples/book-app-project/utils.py` のレビュー
   - `doc-writer` → `@samples/book-app-project/books.py` に docstring を追加する
4. 連携する：`error-handler` を使ってエラーハンドリングのギャップを特定し、その後 `doc-writer` を使って改善されたアプローチをドキュメント化する

**成功基準**：一貫した高品質の出力を生成する 3 つの動作する agent ができあがり、`/agent` で切り替えができること。

<details>
<summary>�� ヒント（クリックして展開）</summary>

**スターターテンプレート**：`.github/agents/` に agent ごとに 1 ファイルを作成してください：

`data-validator.agent.md`:
```markdown
---
description: Analyzes JSON data files for missing or malformed entries
---

You analyze JSON data files for missing or malformed entries.

**Focus areas:**
- Empty or missing author fields
- Invalid years (year=0, future years, negative years)
- Missing required fields (title, author, year, read)
- Duplicate entries
```

`error-handler.agent.md`:
```markdown
---
description: Reviews Python code for error handling consistency
---

You review Python code for error handling consistency.

**Standards:**
- No bare except clauses
- Use custom exceptions where appropriate
- All file operations use context managers
- Consistent return types for success/failure
```

`doc-writer.agent.md`:
```markdown
---
description: Technical writer for clear Python documentation
---

You are a technical writer who creates clear Python documentation.

**Standards:**
- Google-style docstrings
- Include parameter types and return values
- Add usage examples for public methods
- Note any exceptions raised
```

**agent のテスト：**

> �� **注意：** このリポジトリのローカルコピーにはすでに `samples/book-app-project/data.json` があるはずです。見つからない場合は、ソースリポジトリからオリジナル版をダウンロードしてください：
> [data.json](https://github.com/github/copilot-cli-for-beginners/blob/main/samples/book-app-project/data.json)

```bash
copilot
> /agent
# Select "data-validator" from the list
> @samples/book-app-project/data.json 著者フィールドが空の本や無効な発行年がある本を確認してください
```

**ヒント：** YAML frontmatter の `description` フィールドは agent が動作するために必須です。

</details>

### ボーナスチャレンジ：Instruction ライブラリ

必要に応じて呼び出す agent を構築しました。次は反対側を試してみましょう：Copilot がすべてのセッションで自動的に読み込む **instruction ファイル** です。`/agent` は不要です。

少なくとも 3 つの instruction ファイルを含む `.github/instructions/` フォルダーを作成してください：
- `python-style.instructions.md`：PEP 8 と型ヒントの規約を強制するため
- `test-standards.instructions.md`：テストファイルで pytest の規約を強制するため
- `data-quality.instructions.md`：JSON データエントリのバリデーションのため

各 instruction ファイルを本のアプリのコードでテストしてください。

---

<details>
<summary>🔧 <strong>よくあるミスとトラブルシューティング</strong>（クリックして展開）</summary>

### よくあるミス

| ミス | 発生する問題 | 対処法 |
|---------|--------------|-----|
| agent frontmatter に `description` がない | agent が読み込まれないか、検出されない | YAML frontmatter に常に `description:` を含めてください |
| agent のファイルの場所が間違っている | 使用しようとしたときに agent が見つからない | `~/.copilot/agents/`（個人用）または `.github/agents/`（プロジェクト用）に配置してください |
| `.agent.md` の代わりに `.md` を使用している | ファイルが agent として認識されない可能性がある | `python-reviewer.agent.md` のようにファイルに名前を付けてください |
| agent のプロンプトが長すぎる | 30,000 文字の制限に達する可能性がある | agent の定義を簡潔に保ち、詳細な instructions には skill を使用してください |

### トラブルシューティング

**Agent が見つからない** - 以下のいずれかの場所に agent ファイルが存在することを確認してください：
- `~/.copilot/agents/`
- `.github/agents/`

利用可能な agent を一覧表示する：

```bash
copilot
> /agent
# Shows all available agents
```

**Agent が instructions に従わない** - プロンプトを明確にし、agent の定義にさらに詳細を追加してください：
- バージョンを含む特定のフレームワーク/ライブラリ
- チームの規約
- サンプルコードパターン

**カスタム instructions が読み込まれない** - プロジェクトで `/init` を実行して、プロジェクト固有の instructions を設定してください：

```bash
copilot
> /init
```

または、無効になっていないか確認してください：
```bash
# Don't use --no-custom-instructions if you want them loaded
copilot  # This loads custom instructions by default
```

</details>

---

# まとめ

## 🔑 重要なポイント

1. **組み込み agent**：`/plan` と `/review` は直接呼び出す。Explore と Task は自動的に動作する
2. **カスタム agent** は `.agent.md` ファイルで定義された専門家です
3. **優れた agent** は明確な専門知識、標準、出力フォーマットを持っています
4. **マルチ agent の連携** は専門知識を組み合わせることで複雑な問題を解決します
5. **Instruction ファイル**（`.instructions.md`）はチームの標準を自動的に適用するためにエンコードします
6. **一貫した出力** は明確に定義された agent instructions から生まれます

> 📋 **クイックリファレンス**：コマンドとショートカットの完全なリストについては、[GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference) を参照してください。

---

## ➡️ 次のステップ

Agent は Copilot がコードに対して *どのようにアプローチし、的を絞ったアクションを取るか* を変えます。次に、**skill** について学びます。Skill は *どのようなステップ* を踏むかを変えます。agent と skill の違いが気になりますか？Chapter 05 でそれを正面から取り上げます。

**[Chapter 05: Skills System](../05-skills/README.md)** では、以下について学びます：

- プロンプトから skill が自動的にトリガーされる方法（スラッシュコマンド不要）
- コミュニティ skill のインストール
- SKILL.md ファイルを使ったカスタム skill の作成
- agent、skill、MCP の違い
- それぞれをいつ使うか

---

**[← Chapter 03 に戻る](../03-development-workflows/README.md)** | **[Chapter 05 に進む →](../05-skills/README.md)**
