![Chapter 03: Development Workflows](images/chapter-header.png)

> **AIが自分では気づかなかったバグを見つけてくれたら、どうなるでしょう？**

この章では、GitHub Copilot CLI を日常的なツールとして活用します。テスト、リファクタリング、デバッグ、Gitなど、普段から使っているワークフローの中で利用する方法を学びます。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- Copilot CLI を使ってコードレビューを包括的に行う
- レガシーコードを安全にリファクタリングする
- AIの支援を受けながらバグをデバッグする
- テストを自動生成する
- Copilot CLI を git ワークフローに統合する

> ⏱️ **目安時間**: 約60分（読み取り15分 + 実践45分）

---

## 🧩 現実世界のアナロジー：大工のワークフロー

大工はツールの使い方を知っているだけでなく、仕事の種類に応じた*ワークフロー*を持っています：

<img src="images/carpenter-workflow-steps.png" alt="Craftsman workshop showing three workflow lanes: Building Furniture (Measure, Cut, Assemble, Finish), Fixing Damage (Assess, Remove, Repair, Match), and Quality Check (Inspect, Test Joints, Check Alignment)" width="800"/>

同様に、開発者もタスクの種類に応じたワークフローを持っています。GitHub Copilot CLI はこれらのワークフローを強化し、日々のコーディング作業をより効率的かつ効果的にします。

---

# 5つのワークフロー

<img src="images/five-workflows.png" alt="Five glowing neon icons representing code review, testing, debugging, refactoring, and git integration workflows" width="800"/>

以下の各ワークフローは独立しています。現在のニーズに合ったものを選んで進めるか、すべて順番に取り組んでください。

---

## 自由に選んで進める

この章では、開発者がよく使う5つのワークフローを扱います。**ただし、すべてを一度に読む必要はありません！** 各ワークフローは折りたたみ可能なセクションとして独立しています。現在のプロジェクトに合ったものを選んでください。残りのワークフローはいつでも後から確認できます。

<img src="images/five-workflows-swimlane.png" alt="Five Development Workflows: Code Review, Refactoring, Debugging, Test Generation, and Git Integration shown as horizontal swimlanes" width="800"/>

| やりたいこと | ジャンプ先 |
|---|---|
| マージ前にコードをレビューしたい | [ワークフロー1: コードレビュー](#workflow-1-code-review) |
| 乱雑またはレガシーコードを整理したい | [ワークフロー2: リファクタリング](#workflow-2-refactoring) |
| バグを追跡して修正したい | [ワークフロー3: デバッグ](#workflow-3-debugging) |
| コードのテストを生成したい | [ワークフロー4: テスト生成](#workflow-4-test-generation) |
| より良いコミットと PR を書きたい | [ワークフロー5: Git 統合](#workflow-5-git-integration) |
| コーディング前にリサーチしたい | [クイックヒント：計画やコードを書く前のリサーチ](#quick-tip-research-before-you-plan-or-code) |
| バグ修正ワークフローを最初から最後まで確認したい | [総まとめ](#putting-it-all-together-bug-fix-workflow) |

**以下のワークフローを展開して**、GitHub Copilot CLI がその領域でどのように開発プロセスを強化できるかを確認してください。

---

<a id="workflow-1-code-review"></a>
<details>
<summary><strong>ワークフロー1: コードレビュー</strong> - ファイルのレビュー、/review agent の使用、重要度チェックリストの作成</summary>

<img src="images/code-review-swimlane-single.png" alt="Code review workflow: review, identify issues, prioritize, generate checklist." width="800"/>

### 基本的なレビュー

この例では `@` 記号を使ってファイルを参照し、Copilot CLI がその内容に直接アクセスしてレビューできるようにしています。

```bash
copilot

> @samples/book-app-project/book_app.py のコード品質をレビューしてください
```

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Code Review Demo](images/code-review-demo.gif)

*デモの出力は変わることがあります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

### 入力バリデーションのレビュー

気になるカテゴリをプロンプトに列挙することで、Copilot CLI のレビュー対象を特定の観点（ここでは入力バリデーション）に絞り込めます。

```text
copilot

> @samples/book-app-project/utils.py の入力バリデーションの問題をレビューしてください。不足しているバリデーション、エラーハンドリングのギャップ、エッジケースを確認してください
```


### プロジェクト全体のクロスファイルレビュー

`@` でディレクトリ全体を参照すると、Copilot CLI がプロジェクト内のすべてのファイルを一度にスキャンできます。

```bash
copilot

> @samples/book-app-project/ このプロジェクト全体をレビューしてください。発見した問題を重要度別に分類したマークダウンのチェックリストを作成してください
```

### インタラクティブなコードレビュー

マルチターンの会話を使ってより深く掘り下げます。最初に広範囲なレビューをリクエストし、その後セッションを再起動せずにフォローアップの質問ができます。

```bash
copilot

> @samples/book-app-project/book_app.py このファイルを以下の観点でレビューしてください：
> - 入力バリデーション
> - エラーハンドリング
> - コードスタイルとベストプラクティス

# Copilot CLI provides detailed review

> ユーザー入力の処理で、見落としているエッジケースはありますか？

# Copilot CLI shows potential issues with empty strings, special characters

> 発見したすべての問題を重要度順に並べたチェックリストを作成してください

# Copilot CLI generates prioritized action items
```

### レビューチェックリストのテンプレート

Copilot CLI に特定のフォーマット（ここでは重要度別に分類したマークダウンチェックリスト）で出力するよう依頼できます。このチェックリストはそのままIssueに貼り付けられます。

```bash
copilot

> @samples/book-app-project/ をレビューして、以下のカテゴリに分類した問題のマークダウンチェックリストを作成してください：
> - 重大（データ損失リスク、クラッシュ）
> - 高（バグ、誤動作）
> - 中（パフォーマンス、保守性）
> - 低（スタイル、軽微な改善）
```

### git の変更の理解（/review で重要）

`/review` コマンドを使う前に、git における2種類の変更を理解しておく必要があります：

| 変更の種類 | 意味 | 確認方法 |
|-------------|---------------|------------|
| **ステージ済みの変更** | `git add` で次のコミットに含めるよう印をつけたファイル | `git diff --staged` |
| **未ステージの変更** | 変更したがまだ追加していないファイル | `git diff` |

```bash
# クイックリファレンス
git status           # Shows both staged and unstaged
git add file.py      # Stage a file for commit
git diff             # Shows unstaged changes
git diff --staged    # Shows staged changes
```

### /review コマンドの使い方

`/review` コマンドは組み込みの **code-review agent** を呼び出します。このエージェントはステージ済み・未ステージの変更をシグナル対ノイズ比の高い出力で分析するよう最適化されています。自由形式のプロンプトを書く代わりに、スラッシュコマンドで専用の組み込みエージェントを起動します。

```bash
copilot

> /review
# ステージ済み・未ステージの変更に対して code-review agent を起動します
# 集中した実用的なフィードバックを提供します

> /review 認証のセキュリティ問題を確認してください
# Run review with specific focus area
```

> 💡 **ヒント**: code-review agent は変更が保留中のときに最も効果を発揮します。より集中したレビューのために `git add` でファイルをステージしてください。

</details>

---

<a id="workflow-2-refactoring"></a>
<details>
<summary><strong>ワークフロー2: リファクタリング</strong> - コードの再構成、関心の分離、エラーハンドリングの改善</summary>

<img src="images/refactoring-swimlane-single.png" alt="Refactoring workflow: assess code, plan changes, implement, verify behavior." width="800"/>

### シンプルなリファクタリング

> **まずこちらを試してみてください:** `@samples/book-app-project/book_app.py コマンド処理で if/elif チェーンが使われています。辞書ディスパッチパターンにリファクタリングしてください。`

まずはシンプルな改善から始めましょう。ブックアプリで試してみてください。各プロンプトでは `@` ファイル参照と具体的なリファクタリング指示を組み合わせているので、Copilot CLI は何を変更すればよいかを正確に把握できます。

```bash
copilot

> @samples/book-app-project/book_app.py コマンド処理で if/elif チェーンが使われています。辞書ディスパッチパターンにリファクタリングしてください。

> @samples/book-app-project/utils.py すべての関数に型ヒントを追加してください

> @samples/book-app-project/book_app.py 関心の分離を改善するため、本の表示ロジックを utils.py に移動してください
```

> 💡 **リファクタリング初心者の方へ:** 複雑な変換に取り組む前に、型ヒントの追加や変数名の改善といったシンプルなリクエストから始めてみてください。

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Refactor Demo](images/refactor-demo.gif)

*デモの出力は変わることがあります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

### 関心の分離

1つのプロンプトで `@` を使って複数のファイルを参照すると、Copilot CLI がリファクタリングの一環としてファイル間でコードを移動できます。

```bash
copilot

> @samples/book-app-project/utils.py @samples/book-app-project/book_app.py
> utils.py にロジックと混在した print 文があります。表示関数をデータ処理から分離するようリファクタリングしてください。
```

### エラーハンドリングの改善

関連する2つのファイルを提供し、横断的な関心事を説明することで、Copilot CLI が両方に対して一貫した修正を提案できます。

```bash
copilot

> @samples/book-app-project/utils.py @samples/book-app-project/books.py
> これらのファイルのエラーハンドリングに一貫性がありません。カスタム例外を使った統一的なアプローチを提案してください。
```

### ドキュメントの追加

各 docstring に含めるべき内容を箇条書きで詳細に指定します。

```bash
copilot

> @samples/book-app-project/books.py すべてのメソッドに包括的な docstring を追加してください：
> - パラメータの型と説明を含める
> - 戻り値を文書化する
> - 発生する例外を記載する
> - 使用例を追加する
```

### テストを使った安全なリファクタリング

マルチターンの会話で2つの関連するリクエストをつなげます。まずテストを生成し、そのテストを安全網としてリファクタリングを行います。

```bash
copilot

> @samples/book-app-project/books.py リファクタリングの前に、現在の動作に対するテストを生成してください

# Get tests first

> 次に、BookCollection クラスのファイル操作にコンテキストマネージャーを使うようリファクタリングしてください

# Refactor with confidence - tests verify behavior is preserved
```

</details>

---

<a id="workflow-3-debugging"></a>
<details>
<summary><strong>ワークフロー3: デバッグ</strong> - バグの追跡、セキュリティ監査、ファイルを横断した問題のトレース</summary>

<img src="images/debugging-swimlane-single.png" alt="Debugging workflow: understand error, locate root cause, fix, test." width="800"/>

### シンプルなデバッグ

> **まずこちらを試してみてください:** `@samples/book-app-buggy/books_buggy.py データには存在するのに「The Hobbit」を検索しても結果が返らないとユーザーから報告されています。原因をデバッグしてください。`

まずは何が問題かを説明しましょう。バグのあるブックアプリで試せる一般的なデバッグパターンをいくつか紹介します。各プロンプトでは `@` ファイル参照と明確な症状の説明を組み合わせているので、Copilot CLI がバグを特定して診断できます。

```bash
copilot

# Pattern: "Expected X but got Y"
> @samples/book-app-buggy/books_buggy.py データには存在するのに「The Hobbit」を検索しても結果が返らないとユーザーから報告されています。原因をデバッグしてください。

# Pattern: "Unexpected behavior"
> @samples/book-app-buggy/book_app_buggy.py 存在しない本を削除しようとすると、削除されたとアプリが表示します。原因を見つけるのを手伝ってください。

# Pattern: "Wrong results"
> @samples/book-app-buggy/books_buggy.py 1冊の本を既読にすると、すべての本が既読になります。バグはどこにありますか？
```

> 💡 **デバッグのヒント**: *症状*（実際に起きていること）と*期待*（本来こうなるべき動作）を説明してください。Copilot CLI が残りを調べてくれます。

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Fix Bug Demo](images/fix-bug-demo.gif)

*デモの出力は変わることがあります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

### 「バグ探偵」—AIが関連するバグを発見する

これはコンテキストを活かしたデバッグが輝く場面です。バグのあるブックアプリでこのシナリオを試してみてください。`@` でファイル全体を提供し、ユーザーから報告された症状だけを説明します。Copilot CLI は根本原因を追跡し、近くにある別のバグまで発見することがあります。

```bash
copilot

> @samples/book-app-buggy/books_buggy.py
>
> ユーザーから「著者名の一部で検索してもうまく動かない」という報告があります。
> 原因をデバッグしてください
```

**Copilot CLI の動作**:
```
Root Cause: Line 80 uses exact match (==) instead of partial match (in).

Line 80: return [b for b in self.books if b.author == author]

The find_by_author function requires an exact match. Searching for "Tolkien"
won't find books by "J.R.R. Tolkien".

Fix: Change to case-insensitive partial match:
return [b for b in self.books if author.lower() in b.author.lower()]
```

**なぜこれが重要か**: Copilot CLI はファイル全体を読み込み、バグレポートのコンテキストを理解した上で、明確な説明付きの具体的な修正を提示します。

> 💡 **ボーナス**: Copilot CLI はファイル全体を分析するため、依頼していない*他の*問題を発見することがよくあります。たとえば、著者検索を修正する際に `find_book_by_title` の大文字・小文字を区別するバグも指摘してくれることがあります！

### 現実世界のセキュリティに関するサイドバー

自分のコードをデバッグすることも重要ですが、本番アプリケーションのセキュリティ脆弱性を理解することは非常に重要です。次の例を試してみてください：見慣れないファイルを Copilot CLI に渡してセキュリティ監査を依頼します。

```bash
copilot

> @samples/buggy-code/python/user_service.py この Python ユーザーサービスのすべてのセキュリティ脆弱性を見つけてください
```

このファイルは本番アプリで遭遇する現実世界のセキュリティパターンを示しています。

> 💡 **よく出てくるセキュリティ用語:**
> - **SQL インジェクション**: ユーザー入力がデータベースクエリに直接挿入されることで、攻撃者が悪意のあるコマンドを実行できてしまう問題
> - **パラメータ化クエリ**: 安全な代替手段 — プレースホルダー（`?`）でユーザーデータと SQL コマンドを分離する
> - **競合状態 (Race condition)**: 2つの操作が同時に実行され、互いに干渉し合う状態
> - **XSS（クロスサイトスクリプティング）**: 攻撃者がウェブページに悪意のあるスクリプトを注入する攻撃

---

### エラーの理解

スタックトレースを `@` ファイル参照と一緒にプロンプトに貼り付けると、Copilot CLI がエラーをソースコードに対応付けることができます。

```bash
copilot

> 次のエラーが発生しています：
> AttributeError: 'NoneType' object has no attribute 'title'
>     at show_books (book_app.py:19)
>
> @samples/book-app-project/book_app.py 原因と修正方法を説明してください
```

### テストケースを使ったデバッグ

正確な入力と観察された出力を説明することで、Copilot CLI が推論できる具体的かつ再現可能なテストケースを提供できます。

```bash
copilot

> @samples/book-app-buggy/books_buggy.py remove_book 関数にバグがあります。「Dune」を削除しようとすると、「Dune Messiah」も削除されます。根本原因を説明して修正方法を提示してください。
```

### コードをまたいだ問題のトレース

複数のファイルを参照し、それらをまたいでデータフローを追うよう Copilot CLI に依頼することで、問題の発生源を特定できます。

```bash
copilot

> 本のリストの番号が1ではなく0から始まるとユーザーから報告されています。
> @samples/book-app-buggy/book_app_buggy.py @samples/book-app-buggy/books_buggy.py
> リスト表示のフローをトレースして、問題が発生している箇所を特定してください
```

### データの問題を理解する

コードを読むデータファイルと一緒に提供することで、Copilot CLI がエラーハンドリングの改善を提案する際に全体像を把握できます。

```bash
copilot

> @samples/book-app-project/data.json @samples/book-app-project/books.py
> JSON ファイルが壊れてアプリがクラッシュすることがあります。どのように gracefully に処理すればいいですか？
```

</details>

---

<a id="workflow-4-test-generation"></a>
<details>
<summary><strong>ワークフロー4: テスト生成</strong> - 包括的なテストとエッジケースを自動生成する</summary>

<img src="images/test-gen-swimlane-single.png" alt="Test Generation workflow: analyze function, generate tests, include edge cases, run." width="800"/>

> **まずこちらを試してみてください:** `@samples/book-app-project/books.py すべての関数のテストをエッジケースも含めて pytest で生成してください`

### 「テスト爆発」— 2つのテストから15件以上へ

手動でテストを書く場合、開発者は通常2〜3件の基本的なテストを作成します：
- 有効な入力のテスト
- 無効な入力のテスト
- エッジケースのテスト

Copilot CLI に包括的なテストを生成するよう依頼するとどうなるかを見てみましょう！このプロンプトでは `@` ファイル参照と構造化された箇条書きリストを組み合わせて、Copilot CLI に徹底的なテストカバレッジを促しています：

```bash
copilot

> @samples/book-app-project/books.py 包括的な pytest テストを生成してください。以下のテストを含めてください：
> - 本の追加
> - 本の削除
> - タイトルでの検索
> - 著者での検索
> - 既読にする
> - 空データのエッジケース
```

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Test Generation Demo](images/test-gen-demo.gif)

*デモの出力は変わることがあります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

**得られる結果**: 以下を含む15件以上の包括的なテスト：

```python
class TestBookCollection:
    # Happy path
    def test_add_book_creates_new_book(self):
        ...
    def test_list_books_returns_all_books(self):
        ...

    # Find operations
    def test_find_book_by_title_case_insensitive(self):
        ...
    def test_find_book_by_title_returns_none_when_not_found(self):
        ...
    def test_find_by_author_partial_match(self):
        ...
    def test_find_by_author_case_insensitive(self):
        ...

    # Edge cases
    def test_add_book_with_empty_title(self):
        ...
    def test_remove_nonexistent_book(self):
        ...
    def test_mark_as_read_nonexistent_book(self):
        ...

    # Data persistence
    def test_save_books_persists_to_json(self):
        ...
    def test_load_books_handles_missing_file(self):
        ...
    def test_load_books_handles_corrupted_json(self):
        ...

    # Special characters
    def test_add_book_with_unicode_characters(self):
        ...
    def test_find_by_author_with_special_characters(self):
        ...
```

**結果**: 30秒で、自分で考えて書くと1時間かかるようなエッジケーステストが手に入ります。

---

### ユニットテスト

単一の関数を対象に、テストしたい入力カテゴリを列挙することで、Copilot CLI が集中した徹底的なユニットテストを生成します。

```bash
copilot

> @samples/book-app-project/utils.py get_book_details の包括的な pytest テストを生成してください。以下をカバーしてください：
> - 有効な入力
> - 空文字列
> - 無効な年フォーマット
> - 非常に長いタイトル
> - 著者名に含まれる特殊文字
```

### テストの実行

ツールチェーンについて平易な言葉で質問するだけで、適切なシェルコマンドを生成してくれます。

```bash
copilot

> テストの実行方法を教えてください。pytest のコマンドを示してください。

# Copilot CLI responds:
# cd samples/book-app-project && python -m pytest tests/
# Or for verbose output: python -m pytest tests/ -v
# To see print statements: python -m pytest tests/ -s
```

### 特定シナリオのテスト

カバーしたい高度または複雑なシナリオを列挙することで、Copilot CLI がハッピーパス以上の内容をカバーします。

```bash
copilot

> @samples/book-app-project/books.py 以下のシナリオのテストを生成してください：
> - 重複した本の追加（同じタイトルと著者）
> - タイトルの部分一致による本の削除
> - コレクションが空のときの本の検索
> - 保存時のファイルパーミッションエラー
> - 本コレクションへの同時アクセス
```

### 既存ファイルへのテスト追加

単一の関数に対して*追加の*テストを依頼することで、Copilot CLI が既存のテストを補完する新しいケースを生成します。

```bash
copilot

> @samples/book-app-project/books.py
> find_by_author 関数に対して以下のエッジケースの追加テストを生成してください：
> - ハイフンを含む著者名（例：「Jean-Paul Sartre」）
> - 複数のファーストネームを持つ著者
> - 空文字列を著者として指定する場合
> - アクセント付き文字を含む著者名
```

</details>

---

<a id="workflow-5-git-integration"></a>
<details>
<summary><strong>ワークフロー5: Git 統合</strong> - コミットメッセージ、PR の説明、/pr、/delegate、/diff</summary>

<img src="images/git-integration-swimlane-single.png" alt="Git Integration workflow: stage changes, generate message, commit, create PR." width="800"/>

> 💡 **このワークフローは git の基本的な知識（ステージング、コミット、ブランチ）を前提としています。** git が初めての方は、先に他の4つのワークフローを試してみてください。

### コミットメッセージの生成

> **まずこちらを試してみてください:** `copilot -p "Generate a conventional commit message for: $(git diff --staged)"` — 変更をステージしてからこのコマンドを実行し、Copilot CLI がコミットメッセージを書いてくれる様子を確認してください。

この例では `-p` インラインプロンプトフラグとシェルのコマンド置換を使って、`git diff` の出力を直接 Copilot CLI にパイプし、ワンショットでコミットメッセージを生成します。`$(...)` 構文は括弧内のコマンドを実行してその出力を外側のコマンドに挿入します。

```bash

# See what changed
git diff --staged

# Generate commit message using [Conventional Commit](../GLOSSARY.md#conventional-commit) format
# (structured messages like "feat(books): add search" or "fix(data): handle empty input")
copilot -p "Generate a conventional commit message for: $(git diff --staged)"

# Output: "feat(books): add partial author name search
#
# - Update find_by_author to support partial matches
# - Add case-insensitive comparison
# - Improve user experience when searching authors"
```

---

<details>
<summary>🎬 実際の動作を見る！</summary>

![Git Integration Demo](images/git-integration-demo.gif)

*デモの出力は変わることがあります。お使いのモデル、ツール、レスポンスはここに示されているものと異なる場合があります。*

</details>

---

### 変更内容の説明

`git show` の出力を `-p` プロンプトにパイプすることで、最新のコミットの平易な日本語での要約を得られます。

```bash
# What did this commit change?
copilot -p "Explain what this commit does: $(git show HEAD --stat)"
```

### PR の説明文

`git log` の出力と構造化されたプロンプトテンプレートを組み合わせることで、完全なプルリクエストの説明文を自動生成できます。

```bash
# Generate PR description from branch changes
copilot -p "Generate a pull request description for these changes:
$(git log main..HEAD --oneline)

Include:
- Summary of changes
- Why these changes were made
- Testing done
- Breaking changes? (yes/no)"
```

### インタラクティブモードで /pr を使って現在のブランチの PR を操作する

Copilot CLI のインタラクティブモードでブランチを操作している場合、`/pr` コマンドを使ってプルリクエストを操作できます。`/pr` は PR の表示、新規作成、修正、またはブランチの状態に基づいた自動判断に対応しています。

```bash
copilot

> /pr [view|create|fix|auto]
```

### プッシュ前のレビュー

`-p` プロンプト内で `git diff main..HEAD` を使うことで、ブランチのすべての変更に対してプッシュ前の最終チェックを手早く行えます。

```bash
# Last check before pushing
copilot -p "Review these changes for issues before I push:
$(git diff main..HEAD)"
```

### /delegate でバックグラウンドタスクを委任する

`/delegate` コマンドは作業を GitHub 上の Copilot コーディングエージェントに引き渡します。`/delegate` スラッシュコマンド（または `&` ショートカット）を使って、明確に定義されたタスクをバックグラウンドエージェントに委任できます。

```bash
copilot

> /delegate ログインフォームに入力バリデーションを追加してください

# Or use the & prefix shortcut:
> & README のヘッダーの誤字を修正してください

# Copilot CLI:
# 1. Commits your changes to a new branch
# 2. Opens a draft pull request
# 3. Works in the background on GitHub
# 4. Requests your review when done
```

他の作業に集中しながらこなしたい、明確に定義されたタスクに最適です。

### /diff でセッション中の変更をレビューする

`/diff` コマンドは現在のセッション中に行われたすべての変更を表示します。このスラッシュコマンドを使って、コミットする前に Copilot CLI が変更したすべての内容を視覚的に確認できます。

```bash
copilot

# After making some changes...
> /diff

# Shows a visual diff of all files modified in this session
# Great for reviewing before committing
```

</details>

---

## クイックヒント：計画やコードを書く前のリサーチ

ライブラリを調べたり、ベストプラクティスを理解したり、不慣れなトピックを探索したりする際は、コードを書く前に `/research` を使って詳細なリサーチを行いましょう：

```bash
copilot

> /research CLI アプリでユーザー入力を検証するための最適な Python ライブラリは何ですか？
```

Copilot は GitHub リポジトリとウェブのソースを検索し、参照付きのサマリーを返します。新しい機能を開始する前に情報に基づいた判断をしたい場合に便利です。結果は `/share` を使って共有できます。

> 💡 **ヒント**: `/research` は `/plan` の*前に*使うと効果的です。まずアプローチをリサーチし、それから実装を計画しましょう。

---

## 総まとめ：バグ修正ワークフロー

報告されたバグを修正するための完全なワークフローを紹介します：

```bash

# 1. Understand the bug report
copilot

> Users report: '著者名の一部で本を検索してもうまく動かない'
> @samples/book-app-project/books.py 原因を分析して特定してください

# 2. Debug the issue (continuing in same session)
> 分析に基づいて、find_by_author 関数を見せて問題を説明してください

> find_by_author 関数を修正して部分一致に対応させてください

# 3. Generate tests for the fix
> @samples/book-app-project/books.py 以下を対象とした pytest テストを生成してください：
> - 著者名の完全一致
> - 著者名の部分一致
> - 大文字・小文字を区別しないマッチング
> - 著者名が見つからない場合

# 4. Generate commit message
copilot -p "Generate commit message for: $(git diff --staged)"

# Output: "fix(books): support partial author name search"
```

### バグ修正ワークフローのまとめ

| ステップ | アクション | Copilot コマンド |
|------|--------|-----------------|
| 1 | バグを理解する | `> [バグを説明] @relevant-file.py Analyze the likely cause` |
| 2 | 詳細な分析を得る | `> Show me the function and explain the issue` |
| 3 | 修正を実装する | `> Fix the [specific issue]` |
| 4 | テストを生成する | `> Generate tests for [specific scenarios]` |
| 5 | コミットする | `copilot -p "Generate commit message for: $(git diff --staged)"` |

---

# 練習

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

これらのワークフローを実際に試してみましょう。

---

## ▶️ 自分で試してみる

デモを終えたら、以下のバリエーションを試してみてください：

1. **バグ探偵チャレンジ**: `samples/book-app-buggy/books_buggy.py` の `mark_as_read` 関数をデバッグするよう Copilot CLI に依頼してください。1冊だけでなくすべての本に「読了」マークが付いてしまう理由を説明してくれましたか？

2. **テストチャレンジ**: ブックアプリの `add_book` 関数のテストを生成してください。自分では思いつかなかったエッジケースをいくつ Copilot CLI が含めたか数えてみましょう。

3. **コミットメッセージチャレンジ**: ブックアプリのファイルに小さな変更を加えてステージし（`git add .`）、次のコマンドを実行してください：
   ```bash
   copilot -p "Generate a conventional commit message for: $(git diff --staged)"
   ```
   すぐに自分で書く場合よりも良いメッセージが生成されましたか？

**自己確認**: 「このバグをデバッグして」が「バグを探して」より強力な理由（コンテキストが重要！）を説明できれば、開発ワークフローを理解できています。

---

## 📝 課題

### メインチャレンジ：リファクタリング、テスト、そしてリリースへ

ハンズオンの例では `find_book_by_title` とコードレビューに焦点を当てました。今度は `book-app-project` の別の関数で同じワークフロースキルを練習してみましょう：

1. **レビュー**: `books.py` の `remove_book()` についてエッジケースと潜在的な問題をレビューするよう Copilot CLI に依頼してください：
   `@samples/book-app-project/books.py Review the remove_book() function. What happens if the title partially matches another book (e.g., "Dune" vs "Dune Messiah")? Are there any edge cases not handled?`
2. **リファクタリング**: 大文字・小文字を区別しないマッチングや本が見つからない場合の有用なフィードバックを返すなど、エッジケースに対応するよう `remove_book()` を改善するよう Copilot CLI に依頼してください
3. **テスト**: 改善した `remove_book()` 関数に対して以下をカバーする pytest テストを生成してください：
   - 存在する本を削除する
   - 大文字・小文字を区別しないタイトルマッチング
   - 本が存在しない場合に適切なフィードバックを返す
   - 空のコレクションから削除する
4. **レビュー**: 変更をステージして `/review` を実行し、残っている問題がないか確認してください
5. **コミット**: 次のコマンドで conventional commit メッセージを生成してください：
   `copilot -p "Generate a conventional commit message for: $(git diff --staged)"`

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**各ステップのサンプルプロンプト:**

```bash
copilot

# Step 1: Review
> @samples/book-app-project/books.py remove_book() 関数をレビューしてください。対処されていないエッジケースはありますか？

# Step 2: Refactor
> remove_book() を改善して大文字・小文字を区別しないマッチングを使用し、本が見つからない場合に明確なメッセージを返すようにしてください。変更前後のコードを示してください。

# Step 3: Test
> 改善した remove_book() 関数の pytest テストを生成してください。以下を含めてください：
> - 存在する本を削除する
> - 大文字・小文字を区別しないマッチング（「dune」で「Dune」が削除される）
> - 本が見つからない場合に適切なレスポンスを返す
> - 空のコレクションから削除する

# Step 4: Review
> /review

# Step 5: Commit
> このリファクタリングの conventional commit メッセージを生成してください
```

**ヒント:** `remove_book()` を改善した後、Copilot CLI に「このファイルの他の関数も同様の改善が必要ですか？」と聞いてみてください。`find_book_by_title()` や `find_by_author()` への同様の変更が提案されるかもしれません。

</details>

### ボーナスチャレンジ：Copilot CLI でアプリケーションを作成する

> 💡 **注意**: この GitHub Skills の演習は Python ではなく **Node.js** を使用します。実践するコマンドの発行、コードの生成、ターミナルからのコラボレーションといった GitHub Copilot CLI のテクニックはどの言語にも応用できます。

この演習では、GitHub Copilot CLI を使って Issue を作成し、コードを生成し、ターミナルからコラボレーションしながら Node.js の電卓アプリを構築する方法を学びます。CLI のインストール、テンプレートや agent の使用、イテレーティブなコマンドライン駆動開発を実践します。

##### <img src="../images/github-skills-logo.png" width="28" align="center" /> [「Copilot CLI でアプリケーションを作成する」Skills 演習を始める](https://github.com/skills/create-applications-with-the-copilot-cli)

---

<details>
<summary>🔧 <strong>よくある間違いとトラブルシューティング</strong>（クリックして展開）</summary>

### よくある間違い

| 間違い | 何が起きるか | 修正方法 |
|---------|--------------|-----|
| 「このコードをレビューして」のような曖昧なプロンプトを使う | 特定の問題を見逃す汎用的なフィードバックが返される | 具体的に指定する：「SQL インジェクション、XSS、認証の問題をレビューして」 |
| コードレビューに `/review` を使わない | 最適化された code-review agent を利用できない | シグナル対ノイズ比の高い出力に調整された `/review` を使う |
| コンテキストなしに「バグを探して」と依頼する | Copilot CLI はどんなバグが発生しているか分からない | 症状を説明する：「Y をしたとき X が発生するとユーザーから報告がある」 |
| フレームワークを指定せずにテストを生成する | 間違った構文やアサーションライブラリが使われる可能性がある | 指定する：「Jest を使ってテストを生成して」または「pytest を使って」 |

### トラブルシューティング

**レビューが不完全に見える** — 何を探すべきかより具体的に指定してください：

```bash
copilot

# Instead of:
> @samples/book-app-project/book_app.py をレビューしてください

# Try:
> @samples/book-app-project/book_app.py の入力バリデーション、エラーハンドリング、エッジケースをレビューしてください
```

**テストがフレームワークに合わない** — フレームワークを指定してください：

```bash
copilot

> @samples/book-app-project/books.py pytest を使ってテストを生成してください（unittest は使わないでください）
```

**リファクタリングで動作が変わってしまう** — 動作を維持するよう Copilot CLI に依頼してください：

```bash
copilot

> @samples/book-app-project/book_app.py コマンド処理を辞書ディスパッチにリファクタリングしてください。重要：外部の動作を完全に維持し、破壊的変更は加えないでください
```

</details>

---

# まとめ

## 🔑 主なポイント

<img src="images/specialized-workflows.png" alt="Specialized Workflows for Every Task: Code Review, Refactoring, Debugging, Testing, and Git Integration" width="800"/>

1. **コードレビュー**は具体的なプロンプトで包括的なものになります
2. **リファクタリング**は最初にテストを生成することでより安全になります
3. **デバッグ**はエラーとコードの両方を Copilot CLI に見せることで効果が上がります
4. **テスト生成**にはエッジケースとエラーシナリオを含めるべきです
5. **Git 統合**でコミットメッセージと PR の説明文を自動化できます

> 📋 **クイックリファレンス**: コマンドとショートカットの完全な一覧は [GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference) をご確認ください。

---

## ✅ チェックポイント：基本をマスターしました

**おめでとうございます！** GitHub Copilot CLI を活用するための基本スキルがすべて身につきました：

| スキル | 章 | できるようになったこと |
|-------|---------|----------------|
| 基本コマンド | 第1章 | インタラクティブモード、プランモード、プログラマティックモード（-p）、スラッシュコマンドの使用 |
| コンテキスト | 第2章 | `@` でのファイル参照、セッション管理、コンテキストウィンドウの理解 |
| ワークフロー | 第3章 | コードレビュー、リファクタリング、デバッグ、テスト生成、git との統合 |

第4〜6章では、さらに多くの機能を追加するオプション機能を扱います。ぜひ学んでみてください。

---

## 🛠️ 自分だけのワークフローを構築する

GitHub Copilot CLI の「正しい」使い方は一つではありません。自分のパターンを磨くためのヒントをいくつか紹介します：

> 📚 **公式ドキュメント**: GitHub からの推奨ワークフローとヒントは [Copilot CLI のベストプラクティス](https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices) をご覧ください。

- **些細でないことには `/plan` から始める。** 実行前にプランを磨きましょう — 良いプランは良い結果につながります。
- **効果的なプロンプトを保存しておく。** Copilot CLI が間違えたとき、何がよくなかったかメモしておきましょう。これが時間をかけて個人的なプレイブックになります。
- **自由に試してみる。** 長く詳細なプロンプトを好む開発者もいれば、短いプロンプトとフォローアップを好む人もいます。さまざまなアプローチを試し、自然に感じるものを見つけてください。

> 💡 **次の章**: 第4章と第5章では、ベストプラクティスを Copilot CLI が自動的に読み込むカスタム命令と skill に落とし込む方法を学びます。

---

## ➡️ 次のステップ

残りの章では、Copilot CLI の機能を拡張する追加機能を扱います：

| 章 | 内容 | こんな時に役立つ |
|---------|----------------|---------------------|
| 第4章: Agents | 専門的な AI ペルソナを作成する | フロントエンド、セキュリティなどのドメインエキスパートが欲しいとき |
| 第5章: Skills | タスクの指示を自動ロードする | 同じプロンプトを繰り返す機会が多いとき |
| 第6章: MCP | 外部サービスに接続する | GitHub やデータベースのライブデータが必要なとき |

**推奨**: まずコアワークフローを1週間試してから、具体的なニーズが生まれた時点で第4〜6章に戻ってきてください。

---

## 追加トピックへ進む

**[第4章: Agents とカスタム命令](../04-agents-custom-instructions/README.md)**では、以下を学びます：

- 組み込み agent の使い方（`/plan`、`/review`）
- `.agent.md` ファイルで専門化された agent（フロントエンドエキスパート、セキュリティ監査者）を作成する
- マルチエージェントのコラボレーションパターン
- プロジェクト標準のためのカスタム命令ファイル

---

**[← 第2章に戻る](../02-context-conversations/README.md)** | **[第4章へ進む →](../04-agents-custom-instructions/README.md)**
