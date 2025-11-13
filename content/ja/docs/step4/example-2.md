---
title: "② GitHub Copilot CLI でターミナルから AI を活用する"
categories: [新機能紹介, CLI ツール]
weight: 2
tags: [Copilot CLI, ターミナル, 自動化, CI/CD]
---

## 1. GitHub Copilot CLI とは？

![GitHub Copilot CLI ロゴ](../images/copilot-cli.png)

**GitHub Copilot CLI** は、ターミナルから直接 GitHub Copilot エージェントを利用できる公式のコマンドラインツールです。VS Code などのエディタを開かずに、ターミナル上で AI アシスタントと対話しながらコーディング、デバッグ、GitHub 操作を実行できます。

VS Code 拡張機能との最大の違いは、**スクリプト化・自動化が可能**な点です。プログラマティックモード（`copilot -p`）を使うことで、CI/CD パイプラインやシェルスクリプトから Copilot を呼び出し、開発ワークフローの「外部ループ」を自動化できます。

> **利用可能なプラン**
>
> * GitHub Copilot Pro
> * GitHub Copilot Pro+
> * GitHub Copilot Business
> * GitHub Copilot Enterprise
>
> 組織経由で利用する場合、組織設定で「Copilot CLI ポリシー」が有効になっている必要があります。

> **前提条件**
>
> * Node.js v22 以上
> * npm v10 以上
> * Windows の場合: WSL2 推奨（ネイティブ PowerShell は実験的サポート）

---

## 2. 2つのモードと主な機能

### 2.1 インタラクティブモード vs プログラマティックモード

Copilot CLI は2つのモードで利用できます：

| モード | 起動方法 | 用途 | コマンド例 |
|--------|----------|------|------------|
| **インタラクティブモード** | `copilot` | 対話的にタスクを実行<br>継続的な会話が可能 | `copilot`<br>→ 起動後、自然言語で指示 |
| **プログラマティックモード** | `copilot -p "プロンプト"` | 単一のタスクを一発実行<br>スクリプト・CI/CD から呼び出し可能 | `copilot -p "Summarize commits" --allow-tool 'shell(git)'` |

### 2.2 主な機能

| 機能 | 説明 |
|------|------|
| **ローカルタスク支援** | コードの生成・編集・デバッグ、プロジェクト構造の理解、Git 操作の支援 |
| **GitHub 連携** | Issue/PR の一覧表示、PR 作成・レビュー、GitHub Actions ワークフロー作成 |
| **MCP 拡張** | GitHub MCP サーバー同梱、カスタム MCP サーバー追加で機能拡張可能 |
| **外部ループ統合** | CI/CD パイプライン、スクリプトから呼び出し、開発ワークフローの自動化 |

### 2.3 セキュリティモデル

Copilot CLI はファイルの読み書きやコマンド実行を行うため、セキュリティ設計が重要です：

* **信頼済みディレクトリ**: 初回起動時に、現在のディレクトリを信頼するか確認されます。信頼したディレクトリのみで CLI を使用してください
* **ツール承認システム**: ファイル変更やコマンド実行の前に、都度承認を求められます。セッション中は常に許可する、特定ツールを自動承認する（`--allow-tool`）、特定ツールを拒否する（`--deny-tool`）などのオプションがあります
* **モデル選択**: デフォルトは Claude Sonnet 4.5。`/model` コマンドで変更可能

---

## 3. インストールと基本的な使い方

### 3.1 インストール

#### :pen: 実行コマンド

```bash
# Copilot CLI のインストール
npm install -g @github/copilot

# インストール確認
copilot --version
```

#### :robot: 期待される出力

インストールが成功すると、バージョン情報が表示されます：

```bash
$ copilot --version
0.0.355
```

---

### 3.2 初回起動とセットアップ

#### :pen: 実行コマンド

プロジェクトディレクトリに移動して Copilot CLI を起動します：

```bash
cd /path/to/your-project
copilot
```

**信頼済みディレクトリの確認**

初回起動時に、現在のディレクトリを信頼するか確認されます：

```
Do you trust the files in this directory?

1. Yes, proceed (for this session only)
2. Yes, and remember this folder for future sessions
3. No, exit
```

* **オプション1**: 今回のセッションのみ信頼
* **オプション2**: 今後もこのディレクトリで自動的に信頼（推奨: 信頼できるプロジェクトのみ）
* **オプション3**: キャンセル

> ⚠️ **重要**: 信頼できるディレクトリのみで Copilot CLI を使用してください。不明なコードや機密情報が含まれるディレクトリでは使用を避けてください。

**GitHub 認証**

初回起動時に `/login` コマンドを実行し、GitHub アカウントで認証します：

```
> /login
```

ブラウザが開き、GitHub でのデバイス認証が求められます。画面の指示に従って認証を完了してください。

---

### 3.3 インタラクティブモードでの基本操作

#### :pen: 例題: ファイルの変更履歴を確認

```text
このファイルの過去3回の変更履歴を教えてください
```

#### :robot: 期待される出力

AI が Git ログを確認し、変更履歴を要約します：

```markdown
このファイルの過去3回の変更履歴は以下の通りです：

1. **2025-01-10**: "Add search functionality to ticket list"
   - SearchBox コンポーネントを追加
   - URL パラメータによる検索状態の永続化を実装

2. **2025-01-08**: "Refactor ticket service for better testability"
   - ticket_service.py のロジックを分離
   - テストカバレッジを85%に向上

3. **2025-01-05**: "Fix: Resolve ticket status update bug"
   - ステータス更新時のバリデーションを修正
   - エラーハンドリングを改善

Would you like me to show you the detailed diff for any of these commits?
```

AI がツールを実行する際、承認を求められます：

```
Copilot wants to run: git log --oneline -3 <filename>

1. Yes
2. Yes, and approve 'shell(git)' for the rest of the running session
3. No, and tell Copilot what to do differently (Esc)
```

---

### 3.4 プログラマティックモードでの一発実行

#### :pen: 例題: コミット履歴の要約

```bash
copilot -p "Summarize commits from the last 7 days" --allow-tool 'shell(git)'
```

#### :robot: 期待される出力

AI が過去7日間のコミット履歴を要約し、結果を出力します：

```markdown
直近7日分のコミットは以下の2件です:

1. **a3f5083** (7日前) - Ryusei
   - Require admin for users endpoint; update .gitignore; add Docker platform hint

2. **4ef7782** (7日前) - REDMOND\ryuseioya
   - initial commit
```

`--allow-tool 'shell(git)'` オプションにより、`git` コマンドの実行が自動承認され、対話なしで完了します。

---

### 3.5 外部ループへの応用

💡 **Tips: CI/CD パイプラインとの統合**

プログラマティックモード（`copilot -p`）を活用することで、開発ワークフローの「外部ループ」を自動化できます。将来的には、以下のような自動化が可能になります：

* **セキュリティレビューの自動化**: PR 作成時に `copilot -p "Check for security issues in this PR"` を実行し、脆弱性を自動検出
* **PR 要約の自動生成**: コミット内容を AI が分析し、変更内容のサマリーを自動的に PR コメントに追加
* **ドキュメント更新チェック**: コード変更に追従して README や API 仕様書の更新が必要か AI が判断し、通知
* **チーム通知の高度化**: テスト結果やビルドログを AI が要約し、Slack に自動投稿

例えば、GitHub Actions ワークフローから以下のように呼び出すことで、自動化されたコードレビューが実現できます：

```bash
# GitHub Actions 内での実行例（将来の構想）
copilot -p "Review security concerns in PR #123" --allow-tool 'github' > review.md
```

このように、Copilot CLI は単なる開発支援ツールではなく、**開発プロセス全体を AI で支援する基盤**として活用できる可能性を秘めています。

---

## 4. 練習

以下の練習で Copilot CLI の基本操作を体験しましょう：

### :memo: 練習1: インストールと起動

1. Copilot CLI をインストールしてください（`npm install -g @github/copilot`）
2. 自分のプロジェクトディレクトリで `copilot` を起動してください
3. 信頼済みディレクトリの確認プロンプトで適切なオプションを選択してください
4. `/login` で GitHub 認証を完了してください

### :memo: 練習2: 簡単なファイル操作

インタラクティブモードで以下のプロンプトを実行し、ファイル生成を体験してください：

```text
Create a simple README.md file with project title "My Copilot CLI Test" and description "Testing GitHub Copilot CLI capabilities"
```

AI がファイルを生成する前に、以下の確認が表示されます：

```
Copilot wants to create: README.md

1. Yes
2. Yes, and approve 'write' for the rest of the running session
3. No, and tell Copilot what to do differently (Esc)
```

オプション1を選択し、ファイルが作成されることを確認してください。

### :memo: 練習3: プログラマティックモードの体験

以下のコマンドを実行し、自動化の可能性を体験してください：

```bash
copilot -p "List all files modified in the last 7 days" --allow-tool 'shell(git)'
```

出力結果を確認し、対話なしで自動実行されることを体験してください。

---

## 5. まとめ

### Copilot CLI の3つのメリット

* **ターミナル完結の効率化**: IDE を開かずに、ターミナル上で AI アシスタントと対話しながら開発作業を完結
* **スクリプト化による自動化**: プログラマティックモードにより、シェルスクリプトや CI/CD パイプラインから Copilot を呼び出し可能
* **GitHub 連携の容易さ**: GitHub MCP サーバーが同梱されており、Issue/PR 操作、コードレビューなどを自然言語で実行

### VS Code Copilot との使い分け

| 作業内容 | 推奨ツール | 理由 |
|---------|-----------|------|
| エディタ内でのコード編集 | VS Code Copilot (Edits/Agent Mode) | エディタ統合、マルチファイル編集に優れる |
| ターミナル作業 | **Copilot CLI** | コマンド実行、Git 操作、ログ解析に最適 |
| 自動化・CI/CD | **Copilot CLI** | スクリプト化可能、プログラマティックモード対応 |

### 将来の展望: 外部ループの自動化

Copilot CLI の最大の可能性は、開発ワークフローの「外部ループ」を AI で自動化できる点です：

1. **セキュリティレビューの自動化**: PR 作成時に脆弱性を自動検出し、レビューコメントを生成
2. **PR 通知の高度化**: Slack 連携で変更内容の AI 要約を自動投稿
3. **ドキュメント更新の自動化**: コード変更に追従して README/API 仕様書の更新が必要か判断
4. **チーム全体のコード品質向上**: 統一されたレビュー基準を AI で自動実施

### セキュリティとベストプラクティス

* **信頼できるディレクトリのみで使用**: 不明なコードや機密情報が含まれるディレクトリでは Copilot CLI を起動しない
* **`--allow-all-tools` の慎重な使用**: 全てのツールを自動承認するオプションは、信頼できる環境でのみ使用
* **組織ポリシーの設定**: 組織で導入する場合、MCP サーバーの許可リスト、ログ監査の仕組みを整備
* **定期的なアップデート**: Copilot CLI はパブリックプレビュー段階で頻繁にアップデートされるため、最新版を使用（`npm update -g @github/copilot`）
* **仮想環境での実験**: 本番環境に導入する前に、テスト環境や VM で動作を確認

---

Copilot CLI を活用して、ターミナルベースの開発ワークフローを AI で加速させ、将来的には開発プロセス全体の自動化を実現しましょう。
