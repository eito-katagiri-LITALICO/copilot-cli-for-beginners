![Chapter 07: Putting It All Together](images/chapter-header.png)

> **これまで学んだすべてがここで結集します。アイデアからマージ済み PR まで、1 つのセッションで完結させましょう。**

この章では、これまで学んできたすべての知識を組み合わせて、完全なワークフローを構築します。マルチエージェントの連携を使って機能を構築し、コミット前にセキュリティ問題を検出する pre-commit フックを設定し、Copilot を CI/CD パイプラインに統合し、1 つのターミナルセッションで機能のアイデアからマージ済み PR まで完成させます。ここで GitHub Copilot CLI が真の力の乗数となります。

> 💡 **注意**: この章では、これまで学んだすべてを組み合わせる方法を紹介します。**生産性を上げるために agent、skill、MCP は必須ではありません（ただし、非常に役立ちます）。** 説明・計画・実装・テスト・レビュー・リリースという基本ワークフローは、Chapter 00-03 の組み込み機能だけでも機能します。

## 🎯 学習目標

この章を終えると、次のことができるようになります：

- agent、skill、MCP (Model Context Protocol) を統合ワークフローで組み合わせる
- マルチツールアプローチを使って完全な機能を構築する
- フックを使った基本的な自動化を設定する
- プロフェッショナルな開発のベストプラクティスを適用する

> ⏱️ **目安時間**: 約 75 分（読書 15 分 + ハンズオン 60 分）

---

## 🧩 現実世界のたとえ：オーケストラ

<img src="images/orchestra-analogy.png" alt="Orchestra Analogy - Unified Workflow" width="800"/>

交響楽団にはたくさんのセクションがあります：
- **弦楽器** は基盤を提供します（コアワークフローのように）
- **金管楽器** は力強さを加えます（専門知識を持つ agent のように）
- **木管楽器** は色彩を加えます（機能を拡張する skill のように）
- **打楽器** はリズムを刻みます（外部システムに接続する MCP のように）

それぞれのセクション単体では限られた音しか出せません。しかし、うまく指揮されると、一緒に素晴らしいものを生み出します。

**この章が教えるのはまさにそれです！**<br>
*指揮者がオーケストラを指揮するように、あなたは agent、skill、MCP を統合ワークフローへと orchestrate します*

コードを修正し、テストを生成し、レビューして PR を作成するシナリオを、すべて 1 つのセッションで実行してみましょう。

---

## アイデアから 1 つのセッションでマージ済み PR へ

エディター・ターミナル・テストランナー・GitHub UI の間を行き来してそのたびにコンテキストを失う代わりに、すべてのツールを 1 つのターミナルセッションで組み合わせることができます。このパターンについては、以下の [インテグレーションパターン](#the-integration-pattern-for-power-users) セクションで詳しく説明します。

```bash
# Start Copilot in interactive mode
copilot

> book app に、read が False の本だけを表示する「未読一覧」コマンドを追加したいです。
> どのファイルを変更する必要がありますか？

# Copilot creates high-level plan...

# SWITCH TO PYTHON-REVIEWER AGENT
> /agent
# Select "python-reviewer"

> @samples/book-app-project/books.py get_unread_books メソッドを設計してください。
> 最善のアプローチは何ですか？

# Python-reviewer agent produces:
# - Method signature and return type
# - Filter implementation using list comprehension
# - Edge case handling for empty collections

# SWITCH TO PYTEST-HELPER AGENT
> /agent
# Select "pytest-helper"

> @samples/book-app-project/tests/test_books.py 未読の本をフィルタリングするためのテストケースを設計してください。

# Pytest-helper agent produces:
# - Test cases for empty collections
# - Test cases with mixed read/unread books
# - Test cases with all books read

# IMPLEMENT
> books.py の BookCollection に get_unread_books メソッドを追加する
> book_app.py に「list unread」コマンドオプションを追加する
> show_help 関数のヘルプテキストを更新する

# TEST
> 新機能の包括的なテストを生成してください

# Multiple tests are generated similar to the following:
# - Happy path (3 tests) — filters correctly, excludes read, includes unread
# - Edge cases (4 tests) — empty collection, all read, none read, single book
# - Parametrized (5 cases) — varying read/unread ratios via @pytest.mark.parametrize
# - Integration (4 tests) — interplay with mark_as_read, remove_book, add_book, and data integrity

# Review the changes
> /review

# If review passes, use /pr to operate on the pull request for the current branch
> /pr [view|create|fix|auto]

# Or ask naturally if you want Copilot to draft it from the terminal
> 「Feature: 未読の本一覧コマンドを追加」というタイトルのプルリクエストを作成してください
```

**従来のアプローチ**: エディター・ターミナル・テストランナー・ドキュメント・GitHub UI の間を行き来します。切り替えのたびにコンテキストが失われ、摩擦が生じます。

**重要な気づき**: あなたはアーキテクトのように専門家に指示を出しました。詳細は彼らが担当し、ビジョンはあなたが担当しました。

> 💡 **さらに進めるには**: このような大規模なマルチステップ計画には、`/fleet` を試して独立したサブタスクを並列実行させてみてください。詳しくは[公式ドキュメント](https://docs.github.com/copilot/concepts/agents/copilot-cli/fleet)をご覧ください。

---

# 追加ワークフロー

<img src="images/combined-workflows.png" alt="People assembling a colorful giant jigsaw puzzle with gears, representing how agents, skills, and MCP combine into unified workflows" width="800"/>

Chapter 04-06 を修了したパワーユーザー向けに、agent・skill・MCP があなたの効率をどのように高めるかを示すワークフローを紹介します。

## インテグレーションパターン

すべてを組み合わせるためのメンタルモデルを紹介します：

<img src="images/integration-pattern.png" alt="The Integration Pattern - A 4-phase workflow: Gather Context (MCP), Analyze and Plan (Agents), Execute (Skills + Manual), Complete (MCP)" width="800"/>

---

## ワークフロー 1：バグの調査と修正

フルツール統合による実践的なバグ修正です：

```bash
copilot

# PHASE 1: Understand the bug from GitHub (MCP provides this)
> イシュー #1 の詳細を取得してください

# Learn: "find_by_author doesn't work with partial names"

# PHASE 2: Research best practice (deep research with web + GitHub sources)
> /research Python で大文字・小文字を区別しない文字列マッチングのベストプラクティス

# PHASE 3: Find related code
> @samples/book-app-project/books.py find_by_author メソッドを見せてください

# PHASE 4: Get expert analysis
> /agent
# Select "python-reviewer"

> このメソッドの部分一致の問題を分析してください

# Agent identifies: Method uses exact equality instead of substring matching

# PHASE 5: Fix with agent guidance
> 小文字比較と 'in' 演算子を使って修正を実装してください

# PHASE 6: Generate tests
> /agent
# Select "pytest-helper"

> 部分一致を含む find_by_author の pytest テストを生成してください
> テストケースを含める：部分名、大文字・小文字のバリエーション、一致なし

# PHASE 7: Commit and PR
> この修正のコミットメッセージを生成してください

> イシュー #1 にリンクするプルリクエストを作成してください
```

---

## ワークフロー 2：コードレビューの自動化（オプション）

> 💡 **このセクションはオプションです。** Pre-commit フックはチームに役立ちますが、生産性を上げるために必須ではありません。始めたばかりであればスキップしても構いません。
>
> ⚠️ **パフォーマンスに関する注意**: このフックはステージングされた各ファイルに対して `copilot -p` を呼び出すため、ファイルごとに数秒かかります。大規模なコミットの場合は、重要なファイルのみに絞るか、代わりに `/review` で手動レビューを実行することを検討してください。

**git フック** は、Git が特定のタイミング（例：コミット直前）に自動的に実行するスクリプトです。これを使ってコードの自動チェックを実行できます。コミットに対して Copilot の自動レビューを設定する方法を紹介します：

```bash
# Create a pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# Get staged files (Python files only)
STAGED=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.py$')

if [ -n "$STAGED" ]; then
  echo "Running Copilot review on staged files..."

  for file in $STAGED; do
    echo "Reviewing $file..."

    # Use timeout to prevent hanging (60 seconds per file)
    # --allow-all auto-approves file reads/writes so the hook can run unattended.
    # Only use this in automated scripts. In interactive sessions, let Copilot ask for permission.
    REVIEW=$(timeout 60 copilot --allow-all -p "Quick security review of @$file - critical issues only" 2>/dev/null)

    # Check if timeout occurred
    if [ $? -eq 124 ]; then
      echo "Warning: Review timed out for $file (skipping)"
      continue
    fi

    if echo "$REVIEW" | grep -qi "CRITICAL"; then
      echo "Critical issues found in $file:"
      echo "$REVIEW"
      exit 1
    fi
  done

  echo "Review passed"
fi
EOF

chmod +x .git/hooks/pre-commit
```

> ⚠️ **macOS ユーザーへ**: `timeout` コマンドは macOS にデフォルトで含まれていません。`brew install coreutils` でインストールするか、タイムアウトガードなしで `timeout 60` を単純な呼び出しに置き換えてください。

> 📚 **公式ドキュメント**: 完全なフック API については [Use hooks](https://docs.github.com/copilot/how-tos/copilot-cli/use-hooks) と [Hooks configuration reference](https://docs.github.com/copilot/reference/hooks-configuration) をご覧ください。
>
> 💡 **組み込みの代替手段**: Copilot CLI には組み込みのフックシステム（`copilot hooks`）もあり、pre-commit などのイベントで自動的に実行できます。上記の手動 git フックは完全な制御を提供し、組み込みシステムは設定がより簡単です。どちらのアプローチがワークフローに合うか、上記のドキュメントを参照して決めてください。

これにより、すべてのコミットに簡易セキュリティレビューが実行されます：

```bash
git add samples/book-app-project/books.py
git commit -m "Update book collection methods"

# Output:
# Running Copilot review on staged files...
# Reviewing samples/book-app-project/books.py...
# Critical issues found in samples/book-app-project/books.py:
# - Line 15: File path injection vulnerability in load_from_file
#
# Fix the issue and try again.
```

---

## ワークフロー 3：新しいコードベースへのオンボーディング

新しいプロジェクトに参加する際、コンテキスト・agent・MCP を組み合わせて素早くキャッチアップできます：

```bash
# Start Copilot in interactive mode
copilot

# PHASE 1: Get the big picture with context
> @samples/book-app-project/ このコードベースのハイレベルなアーキテクチャを説明してください

# PHASE 2: Understand a specific flow
> @samples/book-app-project/book_app.py ユーザーが "python book_app.py add" を実行したときに
> 何が起こるか説明してください

# PHASE 3: Get expert analysis with an agent
> /agent
# Select "python-reviewer"

> @samples/book-app-project/books.py 設計上の問題、欠けているエラーハンドリング、
> または推奨する改善点はありますか？

# PHASE 4: Find something to work on (MCP provides GitHub access)
> 「good first issue」ラベルの付いたオープンなイシューを一覧表示してください

# PHASE 5: Start contributing
> 最も簡単なオープンイシューを選んで、修正計画を概説してください
```

このワークフローは、`@` コンテキスト・agent・MCP を 1 つのオンボーディングセッションに組み合わせており、まさにこの章の前半で紹介したインテグレーションパターンそのものです。

---

# ベストプラクティスと自動化

ワークフローをより効果的にするパターンと習慣を紹介します。

---

## ベストプラクティス

### 1. 分析の前にコンテキストを収集する

分析を依頼する前に、必ずコンテキストを収集してください：

```bash
# Good
> イシュー #42 の詳細を取得してください
> /agent
# Select python-reviewer
> このイシューを分析してください

# Less effective
> /agent
# Select python-reviewer
> ログインのバグを修正してください
# Agent doesn't have issue context
```

### 2. Agent・Skill・カスタム命令の違いを理解する

それぞれのツールには得意な場面があります：

```bash
# Agents: Specialized personas you explicitly activate
> /agent
# Select python-reviewer
> この認証コードのセキュリティ問題をレビューしてください

# Skills: Modular capabilities that auto-activate when your prompt
# matches the skill's description (you must create them first — see Ch 05)
> このコードの包括的なテストを生成してください
# If you have a testing skill configured, it activates automatically

# Custom instructions (.github/copilot-instructions.md): Always-on
# guidance that applies to every session without switching or triggering
```

> 💡 **重要なポイント**: Agent と skill はどちらもコードの分析と生成ができます。本当の違いは**起動方法**にあります。agent は明示的（`/agent`）、skill は自動（プロンプトマッチング）、カスタム命令は常時オンです。

### 3. セッションをフォーカスした状態に保つ

`/rename` でセッションにラベルを付け（履歴から見つけやすくなります）、`/exit` でクリーンに終了させましょう：

```bash
# Good: One feature per session
> /rename list-unread-feature
# Work on list unread
> /exit

copilot
> /rename export-csv-feature
# Work on CSV export
> /exit

# Less effective: Everything in one long session
```

### 4. Copilot でワークフローを再利用可能にする

ワークフローを Wiki に文書化するだけでなく、Copilot が活用できるようにリポジトリに直接エンコードしましょう：

- **カスタム命令** (`.github/copilot-instructions.md`): コーディング標準・アーキテクチャルール・ビルド/テスト/デプロイ手順に関する常時オンのガイダンスです。すべてのセッションが自動的に従います。
- **プロンプトファイル** (`.github/prompts/`): チームで共有できる再利用可能なパラメーター付きプロンプトです。コードレビュー・コンポーネント生成・PR 説明などのテンプレートとして使えます。
- **カスタム agent** (`.github/agents/`): セキュリティレビュアーやドキュメントライターなど、チーム全員が `/agent` で起動できる専門的なペルソナをエンコードします。
- **カスタム skill** (`.github/skills/`): 関連するときに自動起動するステップバイステップのワークフロー命令をパッケージ化します。

> 💡 **メリット**: 新しいチームメンバーは無料でワークフローを手に入れられます。誰かの頭の中ではなく、リポジトリに組み込まれているからです。

---

## ボーナス：プロダクションパターン

これらのパターンはオプションですが、プロフェッショナルな環境で役立ちます。

### PR 説明文ジェネレーター

```bash
# Generate comprehensive PR descriptions
BRANCH=$(git branch --show-current)
COMMITS=$(git log main..$BRANCH --oneline)

copilot -p "Generate a PR description for:
Branch: $BRANCH
Commits:
$COMMITS

Include: Summary, Changes Made, Testing Done, Screenshots Needed"
```

### CI/CD インテグレーション

既存の CI/CD パイプラインを持つチームは、GitHub Actions を使ってすべてのプルリクエストに Copilot レビューを自動化できます。これにはレビューコメントの自動投稿や重大な問題のフィルタリングが含まれます。

> 📖 **詳細はこちら**: 完全な GitHub Actions ワークフロー・設定オプション・トラブルシューティングのヒントについては、[CI/CD インテグレーション](../appendices/ci-cd-integration.md) をご覧ください。

---

# 練習

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

完全なワークフローを実際に練習しましょう。

---

## ▶️ 自分で試してみよう

デモを完了したら、次のバリエーションを試してみてください：

1. **エンドツーエンドチャレンジ**: 小さな機能（例：「未読の本を一覧表示する」や「CSV にエクスポートする」）を選びましょう。完全なワークフローを使用します：
   - `/plan` で計画を立てる
   - agent（python-reviewer、pytest-helper）で設計する
   - 実装する
   - テストを生成する
   - PR を作成する

2. **自動化チャレンジ**: コードレビュー自動化ワークフローの pre-commit フックを設定しましょう。意図的なファイルパスの脆弱性を含むコミットを作成してみてください。ブロックされますか？

3. **あなたのプロダクションワークフロー**: よく行う作業のための独自ワークフローを設計しましょう。チェックリストとして書き出してみてください。skill・agent・フックで自動化できる部分はどこですか？

**自己チェック**: agent・skill・MCP がどのように連携するか、そしてそれぞれをいつ使うかを同僚に説明できれば、コースを修了したと言えます。

---

## 📝 課題

### メインチャレンジ：エンドツーエンド機能

ハンズオンの例では「未読の本を一覧表示する」機能の構築を説明しました。今度は別の機能で完全なワークフローを練習しましょう：**年の範囲で本を検索する**：

1. Copilot を起動してコンテキストを収集します：`@samples/book-app-project/books.py`
2. `/plan Add a "search by year" command that lets users find books published between two years` で計画を立てます
3. `BookCollection` に `find_by_year_range(start_year, end_year)` メソッドを実装します
4. `book_app.py` にユーザーへ開始年と終了年を入力させる `handle_search_year()` 関数を追加します
5. テストを生成します：`@samples/book-app-project/books.py @samples/book-app-project/tests/test_books.py Generate tests for find_by_year_range() including edge cases like invalid years, reversed range, and no results.`
6. `/review` でレビューします
7. README を更新します：`@samples/book-app-project/README.md Add documentation for the new "search by year" command.`
8. コミットメッセージを生成します

作業しながらワークフローを記録しておきましょう。

**成功基準**: Copilot CLI を使って計画・実装・テスト・ドキュメント・レビューを含むアイデアからコミットまでの機能を完成させられた場合、課題達成です。

> 💡 **ボーナス**: Chapter 04 で agent を設定済みの場合は、カスタム agent の作成と使用を試してみましょう。例えば、実装レビュー用のエラーハンドラー agent や README 更新用のドキュメントライター agent を作成してみてください。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**この章の冒頭にある [「アイデアからマージ済み PR へ」](#アイデアから-1-つのセッションでマージ済み-pr-へ) の例のパターンに従いましょう。** 主要なステップは以下のとおりです：

1. `@samples/book-app-project/books.py` でコンテキストを収集する
2. `/plan Add a "search by year" command` で計画を立てる
3. メソッドとコマンドハンドラーを実装する
4. エッジケース（無効な入力・空の結果・逆転した範囲）を含むテストを生成する
5. `/review` でレビューする
6. `@samples/book-app-project/README.md` で README を更新する
7. `-p` でコミットメッセージを生成する

**考慮すべきエッジケース：**
- ユーザーが「2000」と「1990」（逆転した範囲）を入力した場合は？
- 範囲に一致する本がない場合は？
- ユーザーが数値以外の入力をした場合は？

**大切なのは完全なワークフローを練習すること**です：アイデア → コンテキスト → 計画 → 実装 → テスト → ドキュメント → コミット。

</details>

---

<details>
<summary>🔧 <strong>よくある間違い</strong>（クリックして展開）</summary>

| 間違い | 何が起こるか | 対処法 |
|--------|------------|--------|
| 実装に直接飛びつく | 後から修正コストが高い設計上の問題を見逃す | まず `/plan` でアプローチを考える |
| 複数のツールが役立つのに 1 つしか使わない | 遅く、十分でない結果になる | 組み合わせる：分析には agent → 実行には skill → 統合には MCP |
| コミット前にレビューしない | セキュリティ問題やバグが見落とされる | 常に `/review` を実行するか、[pre-commit フック](#ワークフロー-2コードレビューの自動化オプション) を使用する |
| ワークフローをチームと共有しない | 各人が車輪の再発明をする | 共有 agent・skill・命令にパターンを文書化する |

</details>

---

# まとめ

## 🔑 重要なポイント

1. **統合 > 孤立**: ツールを組み合わせて最大の効果を発揮する
2. **まずコンテキスト**: 分析の前に必要なコンテキストを収集する
3. **Agent は分析し、Skill は実行する**: 仕事に合った適切なツールを使う
4. **繰り返しを自動化する**: フックとスクリプトが効率を高める
5. **ワークフローを文書化する**: 共有できるパターンはチーム全体の利益になる

> 📋 **クイックリファレンス**: コマンドとショートカットの完全なリストは [GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference) をご覧ください。

---

## 🎓 コース修了！

おめでとうございます！以下のことを学びました：

| Chapter | 学んだこと |
|---------|-----------|
| 00 | Copilot CLI のインストールとクイックスタート |
| 01 | 3 つのインタラクションモード |
| 02 | @ シンタックスによるコンテキスト管理 |
| 03 | 開発ワークフロー |
| 04 | 専門的な agent |
| 05 | 拡張可能な skill |
| 06 | MCP による外部接続 |
| 07 | 統合されたプロダクションワークフロー |

これで GitHub Copilot CLI を開発ワークフローにおける真の力の乗数として活用できるようになりました。

## ➡️ 次のステップ

学習はここで終わりではありません：

1. **毎日練習する**: 実際の作業に Copilot CLI を使う
2. **カスタムツールを構築する**: 特定のニーズに合わせた agent と skill を作成する
3. **知識を共有する**: チームがこれらのワークフローを採用するのを助ける
4. **最新情報を追う**: 新機能のために GitHub Copilot のアップデートをフォローする

### リソース

- [GitHub Copilot CLI ドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [Community Skills](https://github.com/topics/copilot-skill)

---

**よくできました！素晴らしいものを作りに行きましょう。**

**[← Chapter 06 に戻る](../06-mcp-servers/README.md)** | **[コースホームに戻る →](../README.md)**
