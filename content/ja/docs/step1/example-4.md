---
title: ④ Copilot のカスタマイズ
categories: [GitHub Copilot, Agent Mode]
weight: 4
---

## 1. Copilot のカスタマイズ方法は？
VS Code の GitHub Copilot は、**カスタム指示**や**プロンプトファイル**を使用してAIの応答をプロジェクトの要件に合わせてカスタマイズできます。毎回同じコンテキストを入力する代わりに、ファイルに保存して自動的にすべてのチャットリクエストに含めることが可能です。

> **ポイント**
>
> * **カスタム指示** … コーディング規約や技術スタックに関する共通ガイドライン（**どのように**作業を行うか）
> * **プロンプトファイル** … 特定タスク用の再利用可能なプロンプト（**何を**行うか）
> * **自動適用** … ワークスペース全体またはファイル固有の自動適用
> * **チーム共有** … バージョン管理でチーム全体の標準化

---

## 2. カスタマイズの種類と使い分け

VS Code では以下の3つの方法でCopilotをカスタマイズできます：

| 種類 | 用途 | ファイル形式 | 適用範囲 | 使い分けのポイント |
|------|------|-------------|----------|-------------------|
| **`.github/copilot-instructions.md`** | 全般的なコーディング規約 | Markdown | ワークスペース全体 | • プロジェクト全体の基本ルール<br>• すべてのチャットに自動適用<br>• チーム共有が容易 |
| **`.instructions.md`** | タスク・技術固有の指示 | Markdown | globパターンで指定 | • 特定ファイルタイプや領域の詳細ルール<br>• 条件付き自動適用<br>• 複数ファイルで分割管理 |
| **`.prompt.md`** | 再利用可能なプロンプト | Markdown | 手動実行 | • 複雑なタスクの標準化<br>• 変数を使った柔軟な実行<br>• 頻繁に行う作業の効率化 |

### 使い分けの指針

**📝 基本ルール → `.github/copilot-instructions.md`**
- プロジェクト全体で統一したいコーディング規約
- 技術スタックの指定（React, TypeScript等）
- 基本的なファイル構成やネーミング規則

**🎯 専門領域 → `.instructions.md`**
- フロントエンド/バックエンド固有のルール
- テスト専用の指示
- 特定のファイルパターンにのみ適用したい詳細ルール

**🚀 作業効率化 → `.prompt.md`**
- コンポーネント生成、API作成などの定型作業
- コードレビュー、セキュリティチェックなどの検査作業
- 複数のパラメータが必要な複雑なタスク

---

## 3. カスタム指示（Custom Instructions）

### 3.1 カスタム指示の作成方法

**手順：**
![ツールの選択方法](../images/custom-instructions-activate.png)
1. `github.copilot.chat.codeGeneration.useInstructionFiles` 設定を `true` に設定
2. ワークスペースルートに `.github` フォルダを作成（存在しない場合）
3. `.github/copilot-instructions.md` ファイルを作成
4. Markdown形式で指示内容を記述

### 3.2 ファイル構造

#### `.github/copilot-instructions.md`
```
プロジェクトルート/
├── .github/
│   └── copilot-instructions.md  # 📝 全般的なコーディング規約
└── ...
```

**ファイル内容の構造：**
- **ヘッダー（必須）**: なし（Front Matter不要）
- **本文**: Markdown形式で自然言語による指示
- **フォーマット**: 見出し、リスト、コードブロックが利用可能
- **適用範囲**: ワークスペース全体に自動適用

### 3.3 導入方法

カスタム指示ファイル（`.github/copilot-instructions.md`）を作成して、プロジェクト全体のコーディング規約を設定します。

```markdown
# プロジェクトのコーディング規約

## TypeScript
- 厳密な型定義を使用してください
- 非同期処理には async/await を使用してください

## React
- 関数コンポーネントを使用してください
- Tailwind CSS でスタイリングしてください

## テスト
- Jest と React Testing Library を使用してください
```

ファイルを保存すると、以降のすべてのチャットでこれらの指示が自動的に適用されます。

> **💡 Tips: カスタム指示の自動生成**
>
> VS Code の新機能として、**`チャット: ワークスペース指示ファイルを生成する`** コマンドが利用できます。このコマンドは、既存のコードベースを分析して、プロジェクトに最適化されたカスタム指示を自動生成します。
>
> **使用方法:**
> - **コマンドパレット**から `チャット: ワークスペース指示ファイルを生成する` を実行
> ![Instructionsの作成](../images/generate-custom.png)
> - または **チャットビュー**の「歯車アイコン」→「指示の生成」を実行
> ![Instructionsの作成2](../images/generate-custom2.png)
>
> **機能:**
> - プロジェクトの構造、技術スタック、コーディングパターンを分析
> - `.github/copilot-instructions.md` ファイルを自動作成
> - 既存の指示ファイルがある場合は改善提案を生成
>
> 生成された指示は、チームの特定のニーズに合わせてカスタマイズできます。

---

## 4. Instructionsファイル（`.instructions.md`）

### 4.1 作成方法

**手順：**
1. **コマンドパレット**から `チャット: 手順の構成` を実行
![Instructionsの作成](../images/instruction-create.png)
1. または **チャットビュー**の「歯車アイコン」→「指示」→「新しい命令ファイル」
![Instructionsの作成2](../images/instruction-create2.png)
1. 保存場所を選択：
   - **Workspace**: `.github/instructions/` フォルダ（ワークスペース固有）
   - **User profile**: ユーザープロファイル（複数ワークスペース共通）
2. ファイル名を入力（例：`typescript.instructions.md`）

### 4.2 ファイル構造

#### ディレクトリ構造
```
プロジェクトルート/
├── .github/
│   ├── copilot-instructions.md     # 📝 全般指示
│   └── instructions/
│       ├── backend.instructions.md  # 📝 バックエンド専用
│       ├── frontend.instructions.md # 📝 フロントエンド専用
│       └── testing.instructions.md  # 📝 テスト専用
└── ...
```

#### ファイル内容の構造
```markdown
---
description: "TypeScript専用のコーディング指示"
applyTo: "**/*.ts,**/*.tsx"
---

# TypeScript専用指示

## 型定義
- 厳密な型定義を使用してください
- any型の使用を禁止します

## コーディングスタイル
- セミコロンを必須とします
```

**構造要素：**
- **Front Matter**（任意）:
  - `description`: 指示ファイルの説明
  - `applyTo`: 自動適用するファイルのglobパターン
- **本文**: Markdown形式の指示内容

### 4.2 Front Matter の設定項目

| 項目 | 説明 | 例 |
|------|------|-----|
| `description` | 指示ファイルの説明文 | `"TypeScript専用のコーディング指示"` |
| `applyTo` | 自動適用するファイルのglobパターン | `"**/*.ts,**/*.tsx"` |

### 4.3 導入方法

技術領域に特化したinstructionsファイルを作成します。

**テスト専用指示ファイル（`testing.instructions.md`）:**
```markdown
---
description: "テスト作成時の指示"
applyTo: "**/*.test.ts,**/*.spec.ts"
---

# テスト作成指示

## テストライブラリ
- Jest と React Testing Library を使用

## テスト内容
- 正常系と異常系の両方をテスト
- ユーザーの操作に基づいたテストを優先
```

このファイルを保存すると、テストファイル（`.test.ts`、`.spec.ts`）を編集する際に自動的に適用されます。

---

## 5. Promptsファイル（`.prompt.md`）

### 5.1 作成方法

**手順：**
1. **コマンドパレット**から `チャット: プロンプトファイルの構成` を実行
![Promptsの作成](../images/prompt-create.png)
2. または **チャットビュー**の「歯車アイコン」→「プロンプト」→「新しいプロンプトファイル」
![Promptsの作成](../images/prompt-create2.png)
3. 保存場所を選択：
   - **Workspace**: `.github/prompts/` フォルダ（ワークスペース固有）
   - **User profile**: ユーザープロファイル（複数ワークスペース共通）
4. ファイル名を入力（例：`refactor-typescript.prompt.md`）

### 5.2 ファイル構造

#### ディレクトリ構造
```
プロジェクトルート/
├── .github/
│   └── prompts/
│       ├── refactor-typescript.prompt.md    # 📝 TypeScriptリファクタリング用
│       ├── api-review.prompt.md             # 📝 API レビュー用
│       └── security-check.prompt.md         # 📝 セキュリティチェック用
└── ...
```

#### ファイル内容の構造
```markdown
---
mode: agent
model: Claude Sonnet 4
description: "TypeScriptファイルリファクタリングプロンプト"
tools: ["editFiles", "problems", "codebase", "usages"]
---

# TypeScript ファイルのリファクタリング

${file} ファイルをリファクタリングしてください。

## リファクタリング観点
- 型安全性の向上
- パフォーマンスの最適化
- 可読性・保守性の改善
- DRY原則の適用
```

**構造要素：**
- **Front Matter**（任意）:
  - `mode`: チャットモード（`ask`, `edit`, `agent`）
  - `model`: 使用するAIモデル
  - `description`: プロンプトの説明
  - `tools`: エージェントモードで使用可能なツール
- **本文**: プロンプト内容（変数、他ファイル参照可能）

### 5.2 Front Matter の設定項目

| 項目 | 説明 | 例 |
|------|------|-----|
| `mode` | チャットモード | `"agent"`, `"ask"`, `"edit"` |
| `model` | 使用するAIモデル | `"GPT-4.1"`, `"Claude Sonnet 4"` |
| `description` | プロンプトの説明文 | `"TypeScriptファイルリファクタリングプロンプト"` |
| `tools` | 使用可能なツール・ツールセット | `["editFiles", "problems"]` |

### 5.3 導入方法

TypeScriptファイルのリファクタリング用プロンプトファイルを作成します。

**リファクタリング用プロンプト（`refactor-typescript.prompt.md`）:**
```markdown
---
mode: agent
model: GPT-4.1
description: "TypeScriptファイルリファクタリングプロンプト"
tools: ["editFiles", "problems", "codebase"]
---

# TypeScript ファイルのリファクタリング

${file} ファイルをリファクタリングしてください。

## リファクタリング観点
- 型安全性の向上
- パフォーマンスの最適化
- 可読性・保守性の改善
- DRY原則の適用

## 具体的な改善項目
1. any型の除去
2. useCallback/useMemo の適用
3. 重複コードの共通化
4. エラーハンドリングの強化
```

### 5.4 プロンプトファイルの呼び出し方法

作成したプロンプトファイルを実行するには、以下の3つの方法があります：

#### 方法1: コマンドパレットから実行
1. **コマンドパレット**（⇧⌘P / Ctrl+Shift+P）を開く
2. `チャット: プロンプトを実行` コマンドを選択
3. クイックピックからプロンプトファイルを選択

#### 方法2: チャットビューで直接実行
チャット入力欄で `/` に続けてプロンプトファイル名を入力：

```
/refactor-typescript
```

**ファイルを指定してリファクタリング実行：**
```
/refactor-typescript #file:src/components/TodoApp.tsx
```

#### 方法3: エディタの再生ボタンから実行
1. プロンプトファイルをエディタで開く
2. エディタタイトルエリアの**再生ボタン**をクリック
3. 現在のチャットセッションまたは新しいチャットセッションを選択
![エディタの再生ボタン](../images/editor-play-button.png)

> **💡 Tips**: エディタから実行する方法は、プロンプトファイルのテストや反復改善に特に便利です。

---

## 6. 実践的な使用パターン

### 6.1 チーム標準の共有

**推奨ディレクトリ構造:**
```
.github/
├── copilot-instructions.md           # 📝 全般的なコーディング規約
├── instructions/
│   ├── backend.instructions.md       # 📝 バックエンド専用指示
│   ├── frontend.instructions.md      # 📝 フロントエンド専用指示
│   ├── testing.instructions.md       # 📝 テスト専用指示
│   └── database.instructions.md      # 📝 データベース専用指示
└── prompts/
    ├── refactor-typescript.prompt.md    # 📝 TypeScriptリファクタリング用
    ├── create-component.prompt.md       # 📝 コンポーネント作成用
    ├── code-review.prompt.md            # 📝 コードレビュー用
    └── security-check.prompt.md         # 📝 セキュリティチェック用
```

### 6.2 段階的導入のベストプラクティス

**フェーズ1**: 基本的なカスタム指示
1. `.github/copilot-instructions.md` で基本ルールを設定
2. プロジェクト全体に適用される最低限の規約を記述

**フェーズ2**: 専門分野別の指示
1. `frontend.instructions.md`, `backend.instructions.md` を作成
2. 技術領域ごとの詳細な指示を分割

**フェーズ3**: タスク別プロンプト
1. 頻繁に行うタスク用の `.prompt.md` ファイルを作成
2. チーム内で共通化できるプロンプトを蓄積

### :memo: 練習

1. **基本設定**: `.github/copilot-instructions.md` を作成し、基本的なコーディング規約を設定してください

2. **専門指示**: `.instructions.md` ファイルを作成してください
   - テスト用
   - 使用している技術スタック用

3. **再利用プロンプト**: `.prompt.md` ファイルを作成してください
   - TypeScriptリファクタリング用

4. **動作確認**: 作成したファイルが正しく機能することを確認してください

---

## 7. まとめ

Copilotカスタマイゼーションの3つのアプローチ：

| ファイルタイプ | 用途 | 作成方法 | 実践ポイント |
|---------------|------|----------|-------------|
| **`.github/copilot-instructions.md`** | 全般的なコーディング規約 | 手動作成またはワークスペース生成 | • プロジェクト全体の基本ルール<br>• 自動適用<br>• チーム共有しやすい |
| **`.instructions.md`** | タスク・技術固有の指示 | コマンドパレットまたはチャットUI | • globパターンで自動適用<br>• 複数ファイルで分割管理<br>• ワークスペース/ユーザー別 |
| **`.prompt.md`** | 再利用可能なプロンプト | コマンドパレットまたはチャットUI | • 変数使用可能<br>• 手動実行<br>• 複雑なタスクの標準化 |

**成功のポイント:**
- **段階的導入**: 基本→専門→タスク別の順で導入
- **チーム標準化**: バージョン管理でファイルを共有
- **継続的改善**: 使用状況に応じて指示を調整
- **適切な分割**: 1ファイル1目的で管理しやすく
