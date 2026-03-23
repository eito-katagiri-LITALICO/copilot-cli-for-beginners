![Chapter 05: Skills System](images/chapter-header.png)

> **チームのベストプラクティスを毎回説明しなくても、Copilot が自動的に適用してくれるとしたらどうでしょうか？**

この章では、Agent Skills について学びます。Agent Skills とは、タスクに関連するときに Copilot が自動的に読み込む、指示書を格納したフォルダーです。agent が Copilot の*思考方法*を変えるのに対し、skill は Copilot に*タスクを完了するための具体的な方法*を教えます。セキュリティ審査用の skill を作成して Copilot がセキュリティに関する質問を受けたときに自動的に適用されるようにし、コード品質を一貫して保つチーム標準のレビュー基準を構築し、Copilot CLI・VS Code・Copilot coding agent における skill の仕組みを学びます。


## 🎯 学習目標

この章を終えると、以下のことができるようになります。

- Agent Skills の仕組みと使いどころを理解する
- SKILL.md ファイルでカスタム skill を作成する
- 共有リポジトリのコミュニティ skill を活用する
- skill・agent・MCP の使い分けを判断できる

> ⏱️ **所要時間の目安**: 約 55 分（読書 20 分 + ハンズオン 35 分）

---

## 🧩 現実世界のアナロジー：電動工具

汎用のドリルは便利ですが、専用のアタッチメントを付けることで真価を発揮します。
<img src="images/power-tools-analogy.png" alt="Power Tools - Skills Extend Copilot's Capabilities" width="800"/>


Skill も同じ仕組みです。用途に応じてドリルビットを交換するように、Copilot にさまざまなタスク向けの skill を追加できます。

| Skill のアタッチメント | 用途 |
|------------|---------|
| `commit` | 一貫性のあるコミットメッセージを生成する |
| `security-audit` | OWASP の脆弱性を検査する |
| `generate-tests` | 包括的な pytest テストを作成する |
| `code-checklist` | チームのコード品質基準を適用する |



*Skill は Copilot の機能を拡張する専用アタッチメントです*

---

# Skill の仕組み

<img src="images/how-skills-work.png" alt="Glowing RPG-style skill icons connected by light trails on a starfield background representing Copilot skills" width="800"/>

Skill とは何か、なぜ重要なのか、そして agent や MCP との違いについて学びます。

---

## *Skill を初めて使う方へ* まずここから始めましょう！

1. **利用可能な skill を確認する：**
   ```bash
   copilot
   > /skills list
   ```
   プロジェクトとパーソナルフォルダーで Copilot が見つけたすべての skill が表示されます。

2. **実際の skill ファイルを見てみる：** 提供済みの [code-checklist SKILL.md](../.github/skills/code-checklist/SKILL.md) でパターンを確認してください。YAML フロントマターとマークダウンの指示で構成されているだけです。

3. **核となる概念を理解する：** Skill は、プロンプトが skill の description に一致したときに Copilot が*自動的に*読み込む、タスク固有の指示です。有効化する必要はなく、自然に質問するだけです。


## Skill を理解する

Agent Skills は、指示・スクリプト・リソースを含むフォルダーで、**タスクに関連するときに Copilot が自動的に読み込みます**。Copilot はプロンプトを読み取り、一致する skill があるかを確認し、関連する指示を自動的に適用します。

```bash
copilot

> books.py を品質チェックリストで確認してください
# Copilot detects this matches your "code-checklist" skill
# and automatically applies its Python quality checklist

> BookCollection クラスのテストを生成してください
# Copilot loads your "pytest-gen" skill
# and applies your preferred test structure

> このファイルのコード品質の問題は何ですか？
# Copilot loads your "code-checklist" skill
# and checks against your team's standards
```

> 💡 **重要なポイント**：Skill はプロンプトが skill の description に一致することで**自動的にトリガーされます**。自然に質問するだけで、Copilot が裏側で関連する skill を適用します。次に学ぶように、skill を直接呼び出すこともできます。

> 🧰 **すぐに使えるテンプレート**：[.github/skills](../.github/skills/) フォルダーにコピー＆ペーストして試せるシンプルな skill が用意されています。

### スラッシュコマンドによる直接呼び出し

自動トリガーが skill の主な使い方ですが、名前をスラッシュコマンドとして使うことで**skill を直接呼び出す**こともできます。

```bash
> /generate-tests ユーザー認証モジュールのテストを作成してください

> /code-checklist books.py のコード品質の問題を確認してください

> /security-audit API エンドポイントの脆弱性を確認してください
```

特定の skill を確実に使いたい場合に、明示的に制御できます。

> 📝 **Skill と Agent の呼び出しの違い**：skill の呼び出しと agent の呼び出しを混同しないようにしましょう。
> - **Skills**：`/skill-name <prompt>`、例：`/code-checklist Check this file`
> - **Agents**：`/agent`（リストから選択）または `copilot --agent <name>`（コマンドライン）
>
> 同じ名前（例："code-reviewer"）の skill と agent が両方ある場合、`/code-reviewer` と入力すると **skill** が呼び出されます（agent ではありません）。

### Skill が使われたかどうか確認するには

Copilot に直接尋ねることができます。

```bash
> その返答でどの skill を使いましたか？

> セキュリティレビューに使える skill は何がありますか？
```

### Skills・Agents・MCP の比較

Skill は GitHub Copilot の拡張モデルの一部にすぎません。agent や MCP サーバーとの比較を見てみましょう。

> *MCP についてはまだ心配しなくて大丈夫です。[Chapter 06](../06-mcp-servers/) で詳しく説明します。ここでは skill が全体像の中でどこに位置するかを把握するために掲載しています。*

<img src="images/skills-agents-mcp-comparison.png" alt="Comparison diagram showing the differences between Agents, Skills, and MCP Servers and how they combine into your workflow" width="800"/>

| 機能 | 役割 | 使いどころ |
|---------|--------------|-------------|
| **Agents** | AI の思考方法を変える | 多くのタスクにわたる専門知識が必要な場合 |
| **Skills** | タスク固有の指示を提供する | 詳細な手順を持つ特定の繰り返しタスク |
| **MCP** | 外部サービスと接続する | API からのリアルタイムデータが必要な場合 |

幅広い専門知識には agent を、特定タスクの指示には skill を、外部データには MCP を使いましょう。agent は会話中に 1 つ以上の skill を使用できます。たとえば、コードをチェックするよう agent に頼むと、`security-audit` skill と `code-checklist` skill の両方が自動的に適用されることがあります。

> 📚 **さらに詳しく**：skill のフォーマットとベストプラクティスの完全なリファレンスは、公式ドキュメント [About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills) をご覧ください。

---

## 手動プロンプトから自動的な専門知識へ

Skill の作り方に入る前に、*なぜ*学ぶ価値があるのかを見てみましょう。一貫性がもたらす効果を理解すると、「どうやって」の部分がより納得しやすくなります。

### Skill がない場合：一貫性のないレビュー

コードレビューのたびに何かを忘れてしまうことがあります。

```bash
copilot

> このコードの問題をレビューしてください
# Generic review - might miss your team's specific concerns
```

あるいは、毎回長いプロンプトを入力することになります。

```bash
> このコードを以下の点で確認してレビューしてください：bare except 句、型ヒントの欠落、
> ミュータブルなデフォルト引数、ファイル I/O のコンテキストマネージャーの欠落、
> 50行を超える関数、本番コード内の print 文...
```

入力時間：**30 秒以上**。一貫性：**記憶次第で変わる**。

### Skill がある場合：自動的なベストプラクティス

`code-checklist` skill をインストールすれば、自然に質問するだけです。

```bash
copilot

> 本コレクションのコードの品質上の問題を確認してください
```

**裏側で起きること**：
1. Copilot がプロンプトの「code quality」と「issues」を検出する
2. Skill の description を確認し、`code-checklist` skill が一致すると判断する
3. チームの品質チェックリストを自動的に読み込む
4. 自分でリストアップしなくてもすべてのチェックを適用する

<img src="images/skill-auto-discovery-flow.png" alt="How Skills Auto-Trigger - 4-step flow showing how Copilot automatically matches your prompt to the right skill" width="800"/>

*自然に質問するだけです。Copilot がプロンプトに合った skill を見つけて自動的に適用します。*

**出力例**：
```
## Code Checklist: books.py

### Code Quality
- [PASS] All functions have type hints
- [PASS] No bare except clauses
- [PASS] No mutable default arguments
- [PASS] Context managers used for file I/O
- [PASS] Functions are under 50 lines
- [PASS] Variable and function names follow PEP 8

### Input Validation
- [FAIL] User input is not validated - add_book() accepts any year value
- [FAIL] Edge cases not fully handled - empty strings accepted for title/author
- [PASS] Error messages are clear and helpful

### Testing
- [FAIL] No corresponding pytest tests found

### Summary
3 items need attention before merge
```

**違い**：チームの基準が毎回自動的に適用され、入力する手間が省けます。

---

<details>
<summary>🎬 実際の動作を見てみましょう！</summary>

![Skill Trigger Demo](images/skill-trigger-demo.gif)

*デモの出力は異なる場合があります。使用するモデル・ツール・レスポンスは、ここに示されたものと異なる場合があります。*

</details>

---

## 大規模な一貫性：チームの PR レビュー Skill

チームに 10 項目の PR チェックリストがあるとします。Skill がなければ、すべての開発者が 10 項目を覚えなければならず、誰かが必ず 1 つを忘れてしまいます。`pr-review` skill があれば、チーム全体で一貫したレビューができます。

```bash
copilot

> この PR をレビューしてもらえますか？
```

Copilot がチームの `pr-review` skill を自動的に読み込み、10 項目すべてをチェックします。

```
PR Review: feature/user-auth

## Security ✅
- No hardcoded secrets
- Input validation present
- No bare except clauses

## Code Quality ⚠️
- [WARN] print statement on line 45 - remove before merge
- [WARN] TODO on line 78 missing issue reference
- [WARN] Missing type hints on public functions

## Testing ✅
- New tests added
- Edge cases covered

## Documentation ❌
- [FAIL] Breaking change not documented in CHANGELOG
- [FAIL] API changes need OpenAPI spec update
```

**Skill の威力**：すべてのチームメンバーが同じ基準を自動的に適用できます。Skill がチェックリストを処理してくれるため、新メンバーがチェックリストを暗記する必要はありません。

---

# カスタム Skill の作成

<img src="images/creating-managing-skills.png" alt="Human and robotic hands building a wall of glowing LEGO-like blocks representing skill creation and management" width="800"/>

SKILL.md ファイルから独自の skill を作成しましょう。

---

## Skill の保存場所

Skill は `.github/skills/`（プロジェクト固有）または `~/.copilot/skills/`（ユーザーレベル）に保存します。

### Copilot が Skill を見つける方法

Copilot は以下の場所を自動的にスキャンして skill を探します。

| 場所 | スコープ |
|----------|-------|
| `.github/skills/` | プロジェクト固有（git でチームと共有） |
| `~/.copilot/skills/` | ユーザー固有（個人用 skill） |

### Skill の構造

各 skill は独自のフォルダーに `SKILL.md` ファイルを持ちます。必要に応じてスクリプト・例・その他のリソースを含めることもできます。

```
.github/skills/
└── my-skill/
    ├── SKILL.md           # Required: Skill definition and instructions
    ├── examples/          # Optional: Example files Copilot can reference
    │   └── sample.py
    └── scripts/           # Optional: Scripts the skill can use
        └── validate.sh
```

> 💡 **ヒント**：ディレクトリ名は SKILL.md フロントマターの `name`（小文字・ハイフン区切り）に合わせましょう。

### SKILL.md のフォーマット

Skill は YAML フロントマターを持つシンプルなマークダウン形式を使います。

```markdown
---
name: code-checklist
description: Comprehensive code quality checklist with security, performance, and maintainability checks
license: MIT
---

# Code Checklist

When checking code, look for:

## Security
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication/authorization issues
- Sensitive data exposure

## Performance
- N+1 query problems (running one query per item instead of one query for all items)
- Unnecessary loops or computations
- Memory leaks
- Blocking operations

## Maintainability
- Function length (flag functions > 50 lines)
- Code duplication
- Missing error handling
- Unclear naming

## Output Format
Provide issues as a numbered list with severity:
- [CRITICAL] - Must fix before merge
- [HIGH] - Should fix before merge
- [MEDIUM] - Should address soon
- [LOW] - Nice to have
```

**YAML のプロパティ：**

| プロパティ | 必須 | 説明 |
|----------|----------|-------------|
| `name` | **はい** | 一意の識別子（小文字・スペースはハイフン） |
| `description` | **はい** | skill の内容と Copilot がいつ使うべきかの説明 |
| `license` | いいえ | この skill に適用するライセンス |

> 📖 **公式ドキュメント**：[About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills)

### 最初の Skill を作成する

OWASP Top 10 の脆弱性をチェックするセキュリティ審査 skill を構築してみましょう。

```bash
# Create skill directory
mkdir -p .github/skills/security-audit

# Create the SKILL.md file
cat > .github/skills/security-audit/SKILL.md << 'EOF'
---
name: security-audit
description: Security-focused code review checking OWASP (Open Web Application Security Project) Top 10 vulnerabilities
---

# Security Audit

Perform a security audit checking for:

## Injection Vulnerabilities
- SQL injection (string concatenation in queries)
- Command injection (unsanitized shell commands)
- LDAP injection
- XPath injection

## Authentication Issues
- Hardcoded credentials
- Weak password requirements
- Missing rate limiting
- Session management flaws

## Sensitive Data
- Plaintext passwords
- API keys in code
- Logging sensitive information
- Missing encryption

## Access Control
- Missing authorization checks
- Insecure direct object references
- Path traversal vulnerabilities

## Output
For each issue found, provide:
1. File and line number
2. Vulnerability type
3. Severity (CRITICAL/HIGH/MEDIUM/LOW)
4. Recommended fix
EOF

# Test your skill (skills load automatically based on your prompt)
copilot

> @samples/book-app-project/ このコードのセキュリティ脆弱性を確認してください
# Copilot detects "security vulnerabilities" matches your skill
# and automatically applies its OWASP checklist
```

**期待される出力**（実際の結果は異なる場合があります）：

```
Security Audit: book-app-project

[HIGH] Hardcoded file path (book_app.py, line 12)
  File path is hardcoded rather than configurable
  Fix: Use environment variable or config file

[MEDIUM] No input validation (book_app.py, line 34)
  User input passed directly to function without sanitization
  Fix: Add input validation before processing

✅ No SQL injection found
✅ No hardcoded credentials found
```

---

## 良い Skill の description を書く

SKILL.md の `description` フィールドは非常に重要です。Copilot が skill を読み込むかどうかを判断する基準になります。

```markdown
---
name: security-audit
description: Use for security reviews, vulnerability scanning,
  checking for SQL injection, XSS, authentication issues,
  OWASP Top 10 vulnerabilities, and security best practices
---
```

> 💡 **ヒント**：自然に質問するときに使うキーワードを含めましょう。「security review」と言う場合は、description に「security review」を含めてください。

### Skill と Agent を組み合わせる

Skill と agent は連携して動作します。agent が専門知識を提供し、skill が具体的な指示を提供します。

```bash
# Start with a code-reviewer agent
copilot --agent code-reviewer

> book app の品質上の問題を確認してください
# code-reviewer agent's expertise combines
# with your code-checklist skill's checklist
```

---

# Skill の管理と共有

インストール済みの skill を確認し、コミュニティの skill を探し、自分の skill を共有しましょう。

<img src="images/managing-sharing-skills.png" alt="Managing and Sharing Skills - showing the discover, use, create, and share cycle for CLI skills" width="800" />

---

## `/skills` コマンドで Skill を管理する

`/skills` コマンドを使ってインストール済みの skill を管理します。

| コマンド | 説明 |
|---------|--------------|
| `/skills list` | インストール済みのすべての skill を表示する |
| `/skills info <name>` | 特定の skill の詳細を確認する |
| `/skills add <name>` | skill を有効化する（リポジトリやマーケットプレイスから） |
| `/skills remove <name>` | skill を無効化またはアンインストールする |
| `/skills reload` | SKILL.md ファイルを編集後に skill を再読み込みする |

> 💡 **覚えておきましょう**：プロンプトごとに skill を「有効化」する必要はありません。インストールされると、プロンプトが description に一致したときに skill は**自動的にトリガーされます**。これらのコマンドは、使用する skill ではなく、利用可能な skill を管理するためのものです。

### 例：Skill を確認する

```bash
copilot

> /skills list

Available skills:
- security-audit: Security-focused code review checking OWASP Top 10
- generate-tests: Generate comprehensive unit tests with edge cases
- code-checklist: Team code quality checklist
...

> /skills info security-audit

Skill: security-audit
Source: Project
Location: .github/skills/security-audit/SKILL.md
Description: Security-focused code review checking OWASP Top 10 vulnerabilities
```

---

<details>
<summary>実際の動作を見てみましょう！</summary>

![List Skills Demo](images/list-skills-demo.gif)

*デモの出力は異なる場合があります。使用するモデル・ツール・レスポンスは、ここに示されたものと異なる場合があります。*

</details>

---

### `/skills reload` を使うタイミング

Skill の SKILL.md ファイルを作成・編集した後は、Copilot を再起動しなくても `/skills reload` を実行して変更を反映させましょう。

```bash
# Edit your skill file
# Then in Copilot:
> /skills reload
Skills reloaded successfully.
```

> 💡 **知っておくと便利**：`/compact` を使って会話履歴を要約した後も、skill は引き続き有効です。コンパクト化後に再読み込みする必要はありません。

---

## コミュニティ Skill の検索と使用

### Plugin を使って Skill をインストールする

> 💡 **Plugin とは？** Plugin は skill・agent・MCP サーバー設定をまとめてバンドルできるインストール可能なパッケージです。Copilot CLI の「アプリストア」拡張機能のようなものと考えてください。

`/plugin` コマンドを使ってこれらのパッケージを参照・インストールできます。

```bash
copilot

> /plugin list
# Shows installed plugins

> /plugin marketplace
# Browse available plugins

> /plugin install <plugin-name>
# Install a plugin from the marketplace
```

Plugin は複数の機能をまとめてバンドルできます。1 つの plugin に、連携して動作する関連 skill・agent・MCP サーバー設定が含まれることがあります。

### コミュニティの Skill リポジトリ

既製の skill はコミュニティリポジトリからも入手できます。

- **[Awesome Copilot](https://github.com/github/awesome-copilot)** - skill のドキュメントと例を含む GitHub Copilot の公式リソース

### コミュニティ Skill を手動でインストールする

GitHub リポジトリで skill を見つけた場合は、そのフォルダーを skills ディレクトリにコピーしてください。

```bash
# Clone the awesome-copilot repository
git clone https://github.com/github/awesome-copilot.git /tmp/awesome-copilot

# Copy a specific skill to your project
cp -r /tmp/awesome-copilot/skills/code-checklist .github/skills/

# Or for personal use across all projects
cp -r /tmp/awesome-copilot/skills/code-checklist ~/.copilot/skills/
```

> ⚠️ **インストール前に確認しましょう**：skill をプロジェクトにコピーする前に、必ず `SKILL.md` を読んでください。Skill は Copilot の動作を制御するため、悪意のある skill が有害なコマンドの実行や予期しないコードの変更を指示する可能性があります。

---

# 練習

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

学んだことを活かして、独自の skill を構築・テストしてみましょう。

---

## ▶️ 自分でやってみよう

### さらに Skill を作ってみる

異なるパターンを示す 2 つの skill を紹介します。上記の「最初の Skill を作成する」と同じ `mkdir` + `cat` の手順に従うか、適切な場所に skill をコピー＆ペーストしてください。その他の例は [.github/skills](../.github/skills) にあります。

### pytest テスト生成 Skill

コードベース全体で一貫した pytest 構造を確保する skill です。

```bash
mkdir -p .github/skills/pytest-gen

cat > .github/skills/pytest-gen/SKILL.md << 'EOF'
---
name: pytest-gen
description: Generate comprehensive pytest tests with fixtures and edge cases
---

# pytest Test Generation

Generate pytest tests that include:

## Test Structure
- Use pytest conventions (test_ prefix)
- One assertion per test when possible
- Clear test names describing expected behavior
- Use fixtures for setup/teardown

## Coverage
- Happy path scenarios
- Edge cases: None, empty strings, empty lists
- Boundary values
- Error scenarios with pytest.raises()

## Fixtures
- Use @pytest.fixture for reusable test data
- Use tmpdir/tmp_path for file operations
- Mock external dependencies with pytest-mock

## Output
Provide complete, runnable test file with proper imports.
EOF
```

### チームの PR レビュー Skill

チーム全体で一貫した PR レビュー基準を徹底する skill です。

```bash
mkdir -p .github/skills/pr-review

cat > .github/skills/pr-review/SKILL.md << 'EOF'
---
name: pr-review
description: Team-standard PR review checklist
---

# PR Review

Review code changes against team standards:

## Security Checklist
- [ ] No hardcoded secrets or API keys
- [ ] Input validation on all user data
- [ ] No bare except clauses
- [ ] No sensitive data in logs

## Code Quality
- [ ] Functions under 50 lines
- [ ] No print statements in production code
- [ ] Type hints on public functions
- [ ] Context managers for file I/O
- [ ] No TODOs without issue references

## Testing
- [ ] New code has tests
- [ ] Edge cases covered
- [ ] No skipped tests without explanation

## Documentation
- [ ] API changes documented
- [ ] Breaking changes noted
- [ ] README updated if needed

## Output Format
Provide results as:
- ✅ PASS: Items that look good
- ⚠️ WARN: Items that could be improved
- ❌ FAIL: Items that must be fixed before merge
EOF
```

### さらに挑戦する

1. **Skill 作成チャレンジ**：3 点チェックリストを行う `quick-review` skill を作成してください。
   - Bare except 句
   - 型ヒントの欠落
   - 不明瞭な変数名

   "Do a quick review of books.py" と質問してテストしてください。

2. **Skill の比較**：詳細なセキュリティレビューのプロンプトを手動で入力する時間を計測してください。次に「Check for security issues in this file」と質問するだけで security-audit skill が自動的に読み込まれるようにしてみましょう。Skill でどれだけ時間を節約できましたか？

3. **チーム Skill チャレンジ**：チームのコードレビューチェックリストを考えてみましょう。それを skill としてエンコードできますか？Skill が必ず確認すべき 3 つの項目を書き出してください。

**自己確認**：`description` フィールドが重要な理由（Copilot が skill を読み込むかどうかを判断する方法）を説明できれば、skill を理解しています。

---

## 📝 課題

### メインチャレンジ：ブックサマリー Skill を作る

上記の例では `pytest-gen` と `pr-review` skill を作成しました。今度はまったく異なる種類の skill を作成する練習をしましょう。データからフォーマット済みの出力を生成する skill です。

1. 現在の skill を確認する：Copilot を起動し、`/skills list` を入力します。`ls .github/skills/` でプロジェクト skill を確認したり、`ls ~/.copilot/skills/` で個人用 skill を確認したりすることもできます。
2. `.github/skills/book-summary/SKILL.md` に、書籍コレクションのフォーマット済みマークダウンサマリーを生成する `book-summary` skill を作成する
3. Skill に必要な要素：
   - 明確な name と description（description はマッチングに不可欠です！）
   - 具体的なフォーマットルール（例：タイトル・著者・年・既読状態のマークダウンテーブル）
   - 出力規則（例：既読状態に ✅/❌ を使用し、年順にソートする）
4. Skill をテストする：`@samples/book-app-project/data.json Summarize the books in this collection`
5. `/skills list` を確認して skill が自動トリガーされることを検証する
6. `/book-summary Summarize the books in this collection` で直接呼び出してみる

**成功基準**：書籍コレクションについて質問したときに Copilot が自動的に適用する、動作する `book-summary` skill が完成していること。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**スターターテンプレート**：`.github/skills/book-summary/SKILL.md` を作成してください。

```markdown
---
name: book-summary
description: Generate a formatted markdown summary of a book collection
---

# Book Summary Generator

Generate a summary of the book collection following these rules:

1. Output a markdown table with columns: Title, Author, Year, Status
2. Use ✅ for read books and ❌ for unread books
3. Sort by year (oldest first)
4. Include a total count at the bottom
5. Flag any data issues (missing authors, invalid years)

Example:
| Title | Author | Year | Status |
|-------|--------|------|--------|
| 1984 | George Orwell | 1949 | ✅ |
| Dune | Frank Herbert | 1965 | ❌ |

**Total: 2 books (1 read, 1 unread)**
```

**テスト方法：**
```bash
copilot
> @samples/book-app-project/data.json Summarize the books in this collection
# The skill should auto-trigger based on the description match
```

**トリガーされない場合：** `/skills reload` を試してから再度質問してください。

</details>

### ボーナスチャレンジ：コミットメッセージ Skill

1. 一貫したフォーマットで conventional commit メッセージを生成する `commit-message` skill を作成する
2. 変更をステージングして「Generate a commit message for my staged changes」と質問してテストする
3. Skill をドキュメント化し、`copilot-skill` トピックを付けて GitHub で共有する

---

<details>
<summary>🔧 <strong>よくある間違いとトラブルシューティング</strong>（クリックして展開）</summary>

### よくある間違い

| 間違い | 起きること | 修正方法 |
|---------|--------------|-----|
| ファイルを `SKILL.md` 以外の名前にする | Skill が認識されない | ファイルは必ず `SKILL.md` という名前にしてください |
| `description` フィールドが曖昧 | Skill が自動的に読み込まれない | Description は主要な検出メカニズムです。具体的なトリガーワードを使いましょう |
| フロントマターに `name` または `description` がない | Skill の読み込みに失敗する | YAML フロントマターに両方のフィールドを追加してください |
| フォルダーの場所が間違っている | Skill が見つからない | `.github/skills/skill-name/`（プロジェクト）または `~/.copilot/skills/skill-name/`（個人）を使ってください |

### トラブルシューティング

**Skill が使われない** - Copilot が想定通りに skill を使っていない場合：

1. **Description を確認する**：質問の仕方と一致していますか？
   ```markdown
   # Bad: Too vague
   description: Reviews code

   # Good: Includes trigger words
   description: Use for code reviews, checking code quality,
     finding bugs, security issues, and best practice violations
   ```

2. **ファイルの場所を確認する**：
   ```bash
   # Project skills
   ls .github/skills/

   # User skills
   ls ~/.copilot/skills/
   ```

3. **SKILL.md のフォーマットを確認する**：フロントマターは必須です。
   ```markdown
   ---
   name: skill-name
   description: What the skill does and when to use it
   ---

   # Instructions here
   ```

**Skill が表示されない** - フォルダー構造を確認してください。
```
.github/skills/
└── my-skill/           # Folder name
    └── SKILL.md        # Must be exactly SKILL.md (case-sensitive)
```

Skill を作成・編集した後は `/skills reload` を実行して変更が反映されることを確認してください。

**Skill が読み込まれるかテストする** - Copilot に直接尋ねてください。
```bash
> コード品質の確認に使える skill は何がありますか？
# Copilot will describe relevant skills it found
```

**Skill が実際に機能しているか確認するには？**

1. **出力フォーマットを確認する**：Skill が出力フォーマット（`[CRITICAL]` タグなど）を指定している場合、レスポンスにそれが含まれているか確認してください
2. **直接尋ねる**：レスポンスを受け取った後、「Did you use any skills for that?」と質問してください
3. **あり・なしで比較する**：`--no-custom-instructions` で同じプロンプトを試して違いを確認してください。
   ```bash
   # With skills
   copilot --allow-all -p "Review @file.py for security issues"

   # Without skills (baseline comparison)
   copilot --allow-all -p "Review @file.py for security issues" --no-custom-instructions
   ```
4. **特定のチェックを確認する**：Skill に特定のチェック（「50 行を超える関数」など）が含まれている場合、それが出力に現れているか確認してください

</details>

---

# まとめ

## 🔑 重要なポイント

1. **Skill は自動的に動く**：プロンプトが skill の description に一致したときに Copilot が読み込みます
2. **直接呼び出しも可能**：`/skill-name` をスラッシュコマンドとして使って skill を直接呼び出すこともできます
3. **SKILL.md のフォーマット**：YAML フロントマター（name・description・任意の license）とマークダウンの指示で構成されます
4. **場所が重要**：プロジェクト・チーム共有には `.github/skills/`、個人用には `~/.copilot/skills/` を使います
5. **Description が鍵**：自然に質問するときの言い方に合ったキーワードで description を書きましょう

> 📋 **クイックリファレンス**：コマンドとショートカットの完全なリストは [GitHub Copilot CLI command reference](https://docs.github.com/en/copilot/reference/cli-command-reference) をご覧ください。

---

## ➡️ 次のステップ

Skill は自動読み込みされる指示によって Copilot の機能を拡張します。では、外部サービスへの接続はどうでしょうか？それが MCP の出番です。

**[Chapter 06: MCP Servers](../06-mcp-servers/README.md)** では以下を学びます。

- MCP（Model Context Protocol）とは何か
- GitHub・ファイルシステム・ドキュメントサービスへの接続
- MCP サーバーの設定
- 複数サーバーを使ったワークフロー

---

**[← Chapter 04 に戻る](../04-agents-custom-instructions/README.md)** | **[Chapter 06 へ進む →](../06-mcp-servers/README.md)**
