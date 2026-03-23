# AGENTS.md

GitHub Copilot CLI を教える初心者向けコースです。ソフトウェアではなく、教育コンテンツです。

## Structure

| Path | Purpose |
|------|---------|
| `00-07/` | 各章：アナロジー → 概念 → ハンズオン → 課題 → 次へ |
| `samples/book-app-project/` | **主要サンプル**：全章を通じて使用する Python CLI の本コレクションアプリ |
| `samples/book-app-project-cs/` | 本コレクションアプリの C# バージョン |
| `samples/book-app-project-js/` | 本コレクションアプリの JavaScript バージョン |
| `samples/book-app-buggy/` | **意図的なバグ**：デバッグ演習用（第 03 章） |
| `samples/agents/` | agent テンプレートのサンプル（python-reviewer, pytest-helper, hello-world） |
| `samples/skills/` | skill テンプレートのサンプル（code-checklist, pytest-gen, commit-message, hello-world） |
| `samples/mcp-configs/` | MCP サーバー設定のサンプル |
| `samples/buggy-code/` | **オプション追加教材**：セキュリティに焦点を当てたバグのあるコード（JS および Python） |
| `samples/src/` | **オプション追加教材**：旧バージョンのコースから引き継いだ JS/React サンプル |
| `appendices/` | 補足リファレンス資料 |

## Do

- 説明は初心者にわかりやすく保ち、AI/ML の専門用語を使用する場合は説明してください
- bash のサンプルはコピー＆ペーストしてすぐ使えるようにしてください
- トーン：親しみやすく、励ましを与え、実践的に
- 主要なサンプルでは `samples/book-app-project/` のパスを使用してください
- コードサンプルでは Python/pytest のコンテキストを使用してください

## Don't

- `samples/book-app-buggy/` や `samples/buggy-code/` のバグを修正しないでください — 意図的なものです
- README.md のコース目次を更新せずに章を追加しないでください
- 読者が AI/ML の用語を知っていることを前提にしないでください

## Build

```bash
npm install && npm run release
```
