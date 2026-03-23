---
name: commit-message
description: 規約に沿ったコミットメッセージを生成します - コミット作成、コミットメッセージの記述、または git コミットのサポートが必要な際に使用します
---

# Commit Message Skill

Conventional Commits の仕様に従ったコミットメッセージを生成します。

## フォーマット

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

## タイプ一覧

| タイプ | 使用タイミング |
|------|-------------|
| `feat` | 新機能の追加 |
| `fix` | バグの修正 |
| `docs` | ドキュメントのみの変更 |
| `style` | フォーマットの変更（コードの動作に影響しない） |
| `refactor` | バグ修正でも機能追加でもないコードの変更 |
| `perf` | パフォーマンスの改善 |
| `test` | テストの追加または更新 |
| `chore` | メンテナンス作業 |

## ルール

1. サブジェクト行は最大 72 文字以内にする
2. 命令形を使う（"added" や "adds" ではなく "add"）
3. サブジェクト行の末尾にピリオドを付けない
4. サブジェクトと本文の間に空行を挿入する
5. 本文では **何を** そして **なぜ** を説明し、方法は書かない

## 例

シンプルな例：
```
fix(auth): prevent redirect loop on expired sessions
```

本文付きの例：
```
feat(api): add rate limiting to public endpoints

- Limits requests to 100/minute per IP
- Returns 429 status with retry-after header
- Configurable via RATE_LIMIT_MAX env variable

Closes #234
```
