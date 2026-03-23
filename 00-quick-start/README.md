![Chapter 00: Quick Start](images/chapter-header.png)

ようこそ！この章では、GitHub Copilot CLI (Command Line Interface) のインストール、GitHub アカウントでのサインイン、そして動作確認を行います。セットアップのための章です。準備が整ったら、Chapter 01 からいよいよ本格的なデモが始まります！

## 🎯 学習目標

この章を終えると、以下が完了しています：

- GitHub Copilot CLI のインストール
- GitHub アカウントでのサインイン
- 簡単なテストによる動作確認

> ⏱️ **所要時間の目安**: 約10分（読む：5分 ＋ 実践：5分）

---

## ✅ 前提条件

- **Copilot へのアクセス権がある GitHub アカウント**。[サブスクリプションオプションを確認する](https://github.com/features/copilot/plans)。学生・教職員の方は [GitHub Education](https://education.github.com/pack) 経由で Copilot Pro を無料で利用できます。
- **ターミナルの基本操作**: `cd` や `ls` などのコマンドに慣れていること

### 「Copilot へのアクセス権」とは

GitHub Copilot CLI を使用するには、有効な Copilot サブスクリプションが必要です。[github.com/settings/copilot](https://github.com/settings/copilot) でご自身の状況を確認できます。以下のいずれかが表示されているはずです：

- **Copilot Individual** - 個人サブスクリプション
- **Copilot Business** - 組織経由
- **Copilot Enterprise** - エンタープライズ経由
- **GitHub Education** - 認定済みの学生・教職員は無料

「You don't have access to GitHub Copilot」と表示された場合は、無料オプションの利用、プランへの加入、またはアクセス権を提供している組織への参加が必要です。

---

## インストール

> ⏱️ **所要時間の目安**: インストールに2〜5分、認証にさらに1〜2分かかります。

### 推奨: GitHub Codespaces（セットアップ不要）

前提条件のインストールを避けたい場合は、GitHub Codespaces を使用できます。GitHub Copilot CLI がすぐに使える状態で用意されており（サインインが必要です）、Python 3.13、pytest、GitHub CLI がプリインストールされています。

1. [このリポジトリをフォーク](https://github.com/github/copilot-cli-for-beginners/fork) して GitHub アカウントに追加します
2. **Code** > **Codespaces** > **Create codespace on main** を選択します
3. コンテナのビルドが完了するまで数分お待ちください
4. 準備完了です！ターミナルが Codespace 環境で自動的に開きます。

> 💡 **Codespace での確認**: `cd samples/book-app-project && python book_app.py help` を実行して、Python とサンプルアプリが正常に動作していることを確認してください。

### 代替手段: ローカルインストール

> 💡 **どれを選べばよいかわからない場合**: Node.js がインストール済みであれば `npm` を使用してください。それ以外は、お使いのシステムに合ったオプションを選択してください。

> 💡 **デモには Python が必要です**: このコースでは Python のサンプルアプリを使用します。ローカルで作業する場合は、デモを開始する前に [Python 3.10+](https://www.python.org/downloads/) をインストールしてください。

> **注意:** コース全体を通じて主に Python（`samples/book-app-project`）を使用した例を紹介していますが、JavaScript（`samples/book-app-project-js`）および C#（`samples/book-app-project-cs`）バージョンも用意されています。各サンプルには、その言語でアプリを実行するための手順が記載された README が含まれています。

お使いのシステムに合った方法を選択してください：

### 全プラットフォーム (npm)

```bash
# If you have Node.js installed, this is a quick way to get the CLI
npm install -g @github/copilot
```

### macOS/Linux (Homebrew)

```bash
brew install copilot-cli
```

### Windows (WinGet)

```bash
winget install GitHub.Copilot
```

### macOS/Linux (Install Script)

```bash
curl -fsSL https://gh.io/copilot-install | bash
```

---

## 認証

`copilot-cli-for-beginners` リポジトリのルートでターミナルウィンドウを開き、CLI を起動してフォルダーへのアクセスを許可します。

```bash
copilot
```

リポジトリを含むフォルダーを信頼するかどうか確認されます（まだ行っていない場合）。一度だけ信頼するか、今後のすべてのセッションで信頼するかを選択できます。

<img src="images/copilot-trust.png" alt="Trusting files in a folder with the Copilot CLI" width="800"/>

フォルダーを信頼した後、GitHub アカウントでサインインできます。

```
> /login
```

**次に行われること:**

1. Copilot CLI がワンタイムコード（例: `ABCD-1234`）を表示します
2. ブラウザーが GitHub のデバイス認証ページを開きます。まだの場合は GitHub にサインインしてください。
3. 要求されたコードを入力します
4. 「Authorize」を選択して GitHub Copilot CLI へのアクセスを許可します
5. ターミナルに戻ると、サインイン完了です！

<img src="images/auth-device-flow.png" alt="Device Authorization Flow - showing the 5-step process from terminal login to signed-in confirmation" width="800"/>

*デバイス認証フロー: ターミナルでコードが生成され、ブラウザーで確認することで Copilot CLI が認証されます。*

**ヒント**: サインイン状態はセッションをまたいで維持されます。トークンの有効期限が切れるか、明示的にサインアウトしない限り、一度行えば十分です。

---

## 動作確認

### ステップ 1: Copilot CLI をテストする

サインインが完了したら、Copilot CLI が正常に動作しているか確認しましょう。ターミナルで、まだ CLI を起動していない場合は起動してから、次のコマンドを入力してください：

```bash
> こんにちは！何を手伝ってもらえますか？
```

応答を受け取ったら、CLI を終了できます：

```bash
> /exit
```

---

<details>
<summary>🎬 実際の動作を確認する！</summary>

![Hello Demo](images/hello-demo.gif)

*デモの出力はこの例と異なります。使用するモデル、ツール、および応答内容は異なる場合があります。*

</details>

---

**期待される出力**: Copilot CLI の機能一覧を説明する、親しみやすい応答。

### ステップ 2: サンプルの Book App を実行する

このコースでは、CLI を使って全体を通して探索・改善するサンプルアプリを提供しています *（コードは /samples/book-app-project で確認できます）*。始める前に、*Python の書籍コレクション ターミナルアプリ* が正常に動作することを確認してください。お使いのシステムに応じて `python` または `python3` を実行してください。

> **注意:** コース全体を通じて主に Python（`samples/book-app-project`）を使用した例を紹介していますが、JavaScript（`samples/book-app-project-js`）および C#（`samples/book-app-project-cs`）バージョンも用意されています。各サンプルには、その言語でアプリを実行するための手順が記載された README が含まれています。

```bash
cd samples/book-app-project
python book_app.py list
```

**期待される出力**: 「The Hobbit」「1984」「Dune」などを含む5冊の書籍リスト。

### ステップ 3: Copilot CLI で Book App を使ってみる

まず、リポジトリのルートに戻ります（ステップ 2 を実行した場合）：

```bash
cd ../..   # Back to the repository root if needed
copilot 
> @samples/book-app-project/book_app.py は何をしますか？
```

**期待される出力**: Book App のメイン機能とコマンドの概要。

エラーが表示された場合は、下記の [トラブルシューティング](#troubleshooting) セクションを確認してください。

完了したら Copilot CLI を終了できます：

```bash
> /exit
```

---

## ✅ 準備完了！

インストールはこれで完了です。Chapter 01 からいよいよ本番です。Chapter 01 では以下を学習します：

- AI が Book App をレビューし、コード品質の問題を即座に発見する様子を確認する
- Copilot CLI の3つの異なる使い方を学ぶ
- 平易な英語からコードを生成する

**[Chapter 01: First Steps へ進む →](../01-setup-and-first-steps/README.md)**

---

## トラブルシューティング

### "copilot: command not found"

CLI がインストールされていません。別のインストール方法をお試しください：

```bash
# If brew failed, try npm:
npm install -g @github/copilot

# Or the install script:
curl -fsSL https://gh.io/copilot-install | bash
```

### "You don't have access to GitHub Copilot"

1. [github.com/settings/copilot](https://github.com/settings/copilot) で Copilot サブスクリプションがあることを確認してください
2. 職場のアカウントを使用している場合、組織が CLI アクセスを許可しているか確認してください

### "Authentication failed"

再認証を行ってください：

```bash
copilot
> /login
```

### ブラウザーが自動で開かない

手動で [github.com/login/device](https://github.com/login/device) にアクセスし、ターミナルに表示されたコードを入力してください。

### トークンの有効期限切れ

もう一度 `/login` を実行してください：

```bash
copilot
> /login
```

### それでも解決しない場合

- [GitHub Copilot CLI ドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli) を確認してください
- [GitHub Issues](https://github.com/github/copilot-cli/issues) を検索してください

---

## 🔑 重要なポイント

1. **GitHub Codespace はすぐに始められる方法です** - Python、pytest、GitHub Copilot CLI がすべてプリインストールされているため、すぐにデモに取りかかれます
2. **複数のインストール方法** - お使いのシステムに合ったものを選択してください（Homebrew、WinGet、npm、またはインストールスクリプト）
3. **認証は一度だけ** - トークンの有効期限が切れるまでログイン状態は維持されます
4. **Book App は動作します** - コース全体を通じて `samples/book-app-project` を使用します

> 📚 **公式ドキュメント**: インストールオプションと要件については、[Install Copilot CLI](https://docs.github.com/copilot/how-tos/copilot-cli/cli-getting-started) を参照してください。

> 📋 **クイックリファレンス**: コマンドとショートカットの完全なリストについては、[GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference) を参照してください。

---

**[Chapter 01: First Steps へ進む →](../01-setup-and-first-steps/README.md)**
