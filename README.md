![GitHub Copilot CLI for Beginners](./images/copilot-banner.png)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)&ensp;
[![Open project in GitHub Codespaces](https://img.shields.io/badge/Codespaces-Open-blue?style=flat-square&logo=github)](https://codespaces.new/github/copilot-cli-for-beginners?hide_repo_select=true&ref=main&quickstart=true)&ensp;
[![Official Copilot CLI documentation](https://img.shields.io/badge/GitHub-CLI_Documentation-00a3ee?style=flat-square&logo=github)](https://docs.github.com/en/copilot/how-tos/copilot-cli)&ensp;
[![Join AI Foundry Discord](https://img.shields.io/badge/Discord-AI_Community-blue?style=flat-square&logo=discord&color=5865f2&logoColor=fff)](https://aka.ms/foundry/discord)

🎯 [学習内容](#what-youll-learn) &ensp; ✅ [前提条件](#prerequisites) &ensp; 🤖 [Copilot ファミリー](#understanding-the-github-copilot-family) &ensp; 📚 [コース構成](#course-structure) &ensp; 📋 [コマンドリファレンス](#-github-copilot-cli-command-reference)

# GitHub Copilot CLI for Beginners

> **✨ AI を活用したコマンドラインアシスタントで、開発ワークフローを強化する方法を学びましょう。**

GitHub Copilot CLI は、AI アシスタンスをターミナルに直接提供します。ブラウザやコードエディタに切り替えることなく、コマンドラインを離れずに質問の回答、フル機能のアプリケーション生成、コードレビュー、テスト生成、デバッグを行うことができます。

24 時間 365 日利用可能な知識豊富な同僚のようなもので、コードを読み、難解なパターンを説明し、より速く作業できるよう助けてくれます！

このコースは以下の方を対象としています：

- コマンドラインから AI を活用したい **ソフトウェア開発者**
- IDE 統合よりもキーボード主体のワークフローを好む **ターミナルユーザー**
- AI を活用したコードレビューと開発プラクティスを標準化したい **チーム**

<a href="https://aka.ms/githubcopilotdevdays" target="_blank">
  <picture>
    <img src="./images/copilot-dev-days.png" alt="GitHub Copilot Dev Days - Find or host an event" width="100%" />
  </picture>
</a>

## 🎯 What You'll Learn

このハンズオンコースでは、GitHub Copilot CLI をゼロから使いこなせるようになります。全章を通じて 1 つの Python 製ブックコレクションアプリを使い、AI を活用したワークフローで段階的に改善していきます。最終的には、コードレビュー、テスト生成、デバッグ、ワークフローの自動化を — すべてターミナルから — 自信を持って行えるようになります。

**AI の経験は不要です。** ターミナルが使えれば、このコースを学ぶことができます。

**こんな方に最適：** 開発者、学生、ソフトウェア開発の経験がある方。

## ✅ Prerequisites

始める前に、以下をご準備ください：

- **GitHub アカウント**: [無料で作成](https://github.com/signup)<br>
- **GitHub Copilot へのアクセス**: [無料プラン](https://github.com/features/copilot/plans)、[月額サブスクリプション](https://github.com/features/copilot/plans)、または [学生・教員向け無料プラン](https://education.github.com/pack)<br>
- **ターミナルの基礎**: `cd`、`ls`、コマンド実行に慣れていること

## 🤖 Understanding the GitHub Copilot Family

GitHub Copilot は、AI を活用したツールのファミリーへと進化しました。それぞれの利用環境は以下の通りです：

| Product | Where It Runs | Description |
|---------|---------------|----------|
| [**GitHub Copilot CLI**](https://docs.github.com/copilot/how-tos/copilot-cli/cli-getting-started)<br>(このコース) | ターミナル | ターミナルネイティブの AI コーディングアシスタント |
| [**GitHub Copilot**](https://docs.github.com/copilot) | VS Code, Visual Studio, JetBrains など | Agent モード、チャット、インライン補完 |
| [**Copilot on GitHub.com**](https://github.com/copilot) | GitHub | リポジトリについての没入型チャット、agent の作成など |
| [**GitHub Copilot coding agent**](https://docs.github.com/copilot/using-github-copilot/using-copilot-coding-agent-to-work-on-tasks) | GitHub | issue を agent にアサインし、PR を受け取る |

このコースは、AI アシスタンスをターミナルに直接もたらす **GitHub Copilot CLI** に焦点を当てています。

## 📚 Course Structure

![GitHub Copilot CLI Learning Path](images/learning-path.png)

| Chapter | Title | What You'll Build |
|:-------:|-------|-------------------|
| 00 | 🚀 [Quick Start](./00-quick-start/README.md) | インストールと動作確認 |
| 01 | 👋 [First Steps](./01-setup-and-first-steps/README.md) | ライブデモ + 3 つの対話モード |
| 02 | 🔍 [Context and Conversations](./02-context-conversations/README.md) | マルチファイルプロジェクトの分析 |
| 03 | ⚡ [Development Workflows](./03-development-workflows/README.md) | コードレビュー、デバッグ、テスト生成 |
| 04 | 🤖 [Create Specialized AI Assistants](./04-agents-custom-instructions/README.md) | ワークフロー用のカスタム agent |
| 05 | 🛠️ [Automate Repetitive Tasks](./05-skills/README.md) | 自動的に読み込まれる skill |
| 06 | 🔌 [Connect to GitHub, Databases & APIs](./06-mcp-servers/README.md) | MCP server の統合 |
| 07 | 🎯 [Putting It All Together](./07-putting-it-together/README.md) | 完全な機能ワークフロー |

## 📖 How This Course Works

各章は同じパターンに従っています：

1. **実世界のアナロジー**: 身近な比較を通じてコンセプトを理解する
2. **コアコンセプト**: 必須の知識を学ぶ
3. **ハンズオン例**: 実際のコマンドを実行して結果を確認する
4. **課題**: 学んだことを練習する
5. **次のステップ**: 次の章のプレビュー

**コード例はそのまま実行できます。** このコースの各 copilot テキストブロックは、ターミナルにコピーして実行することができます。

## 📋 GitHub Copilot CLI Command Reference

**[GitHub Copilot CLI コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)** には、Copilot CLI を効果的に使うためのコマンドとキーボードショートカットが掲載されています。

## 🙋 Getting Help

- 🐛 **バグを見つけた場合は？** [Issue を開く](https://github.com/github/copilot-cli-for-beginners/issues)
- 🤝 **コントリビュートしたい場合は？** PR を歓迎します！
- 📚 **公式ドキュメント:** [GitHub Copilot CLI ドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)

## License

このプロジェクトは MIT オープンソースライセンスの条件のもとでライセンスされています。完全な条件については [LICENSE](./LICENSE) ファイルをご参照ください。

