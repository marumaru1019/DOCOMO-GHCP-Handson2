---
title: "① GitHub Spec Kit で仕様駆動開発を始める"
categories: [新機能紹介, 仕様駆動開発]
weight: 1
tags: [Spec Kit, SDD, ワークフロー, 自動化]
---

## 1. GitHub Spec Kit とは？
![Spec Kit の画面](../images/spec-kit-overview.png)

**GitHub Spec Kit** は、GitHub が提供する **仕様駆動開発（Spec-Driven Development; SDD）** のツールキットです。「明確な仕様書があれば AI は正しくコードを生成できる」という考えに基づき、AI アシスタントがコーディングしやすい仕様書の作成とタスク分割に焦点を当てています。

> **ポイント**
>
> * **仕様ファースト** … コードよりも先に仕様を定義し、AI が読める形で構造化
> * **AI 駆動の実装** … 明確な仕様書から直接コードを自動生成
> * **オーバーエンジニアリング防止** … 計画フェーズに力点を置き、実装の発散を抑制
> * **マルチエージェント対応** … GitHub Copilot、Claude、Cursor など 11+ の AI に対応
> * **自動ブランチ管理** … `/specify` で自動的にブランチが作成され、1機能 = 1PR の構造を実現

---

## 2. Spec Kit の特徴

### 2.1 仕様駆動開発の思想

AI コーディングの普及に伴い、AI のコーディング力は飛躍的に向上しましたが、同時にオーバーエンジニアリングによる「出力の発散」が課題となっています。Spec Kit は、この問題を**計画フェーズに力点を置く**ことで解決します。

### 2.2 Spec Kit の3つの特徴

| 特徴 | 説明 |
|------|------|
| **仕様ファースト** | コードより先に仕様を定義し、AI が読める形で構造化 |
| **AI 駆動の実装** | 明確な仕様書から直接コードを自動生成 |
| **マルチエージェント対応** | GitHub Copilot、Claude、Cursor など 11+ の AI に対応 |

---

## 3. Spec Kit のコマンド体系

---

### 3.1 全コマンド一覧表

Spec Kit は、8つのスラッシュコマンドで開発プロセスを構造化します。

#### コアコマンド(必須)

| コマンド | 役割 | 実行タイミング | 生成物 |
|---------|------|--------------|--------|
| `/speckit.constitution` | プロジェクト憲法の設定 | プロジェクト開始時(1回のみ) | `.specify/memory/constitution.md` |
| `/speckit.specify` | 要件定義(WHAT) | **機能ごと** | `specs/[ブランチ名]/spec.md` + **自動ブランチ作成** |
| `/speckit.plan` | 技術設計(HOW) | 仕様確定後 | `specs/[ブランチ名]/plan.md` |
| `/speckit.tasks` | タスク分解 | 設計完了後 | `specs/[ブランチ名]/tasks.md` |
| `/speckit.implement` | 実装実行 | 最後 | ソースコード + テスト |

#### オプショナルコマンド（任意）

| コマンド | 役割 | 実行タイミング | 説明 |
|---------|------|--------------|------|
| `/speckit.clarify` | 不明点の明確化 | `/speckit.plan` の前（推奨） | 仕様の曖昧さを構造化された質問で解消 |
| `/speckit.analyze` | 一貫性チェック | `/speckit.implement` の前 | 仕様とタスクの整合性を分析 |
| `/speckit.checklist` | 品質チェックリスト生成 | 任意 | 要件の完全性・明確性・一貫性を検証 |

### 3.2 基本ワークフロー(5ステップ)

実務では、以下の5ステップが基本的な流れとなります:

```
/speckit.constitution (初回のみ)
    ↓
/speckit.specify (機能ごと) → 自動ブランチ作成
    ↓
/speckit.plan (技術設計)
    ↓
/speckit.tasks (タスク分解)
    ↓
/speckit.implement (実装)
```

`/speckit.clarify`、`/speckit.analyze`、`/speckit.checklist` は、必要に応じて追加で実行します。

---

## 4. インストールと初期化

### 4.1 インストール

Spec Kit は Python 製のツールで、`uv` パッケージマネージャー経由でインストールします。

```bash
# uv のインストール(まだインストールしていない場合)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Spec Kit のインストール
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git

# インストール確認
specify
```

### 4.2 プロジェクトの初期化

```bash
# プロジェクト初期化
specify init <プロジェクト名>

# または現在のディレクトリで初期化
specify init . --ai copilot
```

初期化時に以下を選択します:

1. **AI アシスタント**: GitHub Copilot、Claude、Cursor、Gemini など
2. **シェルタイプ**: bash、zsh、sh など

初期化が完了すると、以下のディレクトリ構造が作成されます:

```
.
├── .github/
│   └── prompts/
│       ├── speckit.analyze.prompt.md      # /analyze コマンド定義
│       ├── speckit.checklist.prompt.md    # チェックリスト生成
│       ├── speckit.clarify.prompt.md      # /clarify コマンド定義
│       ├── speckit.constitution.prompt.md # /constitution コマンド定義
│       ├── speckit.implement.prompt.md    # /implement コマンド定義
│       ├── speckit.plan.prompt.md         # /plan コマンド定義
│       ├── speckit.specify.prompt.md      # /specify コマンド定義
│       └── speckit.tasks.prompt.md        # /tasks コマンド定義
├── .specify/
│   ├── memory/
│   │   └── constitution.md                # プロジェクト憲法(開発規約)
│   ├── scripts/
│   │   └── bash/
│   │       ├── check-prerequisites.sh     # 前提条件チェック
│   │       ├── common.sh                  # 共通関数
│   │       ├── create-new-feature.sh      # ブランチ作成スクリプト
│   │       ├── setup-plan.sh              # プラン準備
│   │       └── update-agent-context.sh    # エージェントコンテキスト更新
│   └── templates/
│       ├── agent-file-template.md         # エージェントファイルテンプレート
│       ├── checklist-template.md          # チェックリストテンプレート
│       ├── plan-template.md               # 技術設計テンプレート
│       ├── spec-template.md               # 仕様書テンプレート
│       └── tasks-template.md              # タスク分解テンプレート
└── .vscode/
    └── settings.json                      # VS Code 設定
```

> 💡 **Tips**: `specify init --no-git` を使用すると、Git リポジトリの初期化をスキップできます。既存プロジェクトに導入する場合に便利です。

---


## 5. 実践演習: チケット検索機能の実装

Spec Kit を使って、**PoC-GitHubCopilotApp のチケット管理システムに検索機能を追加**します。公式の7ステップフローに従って、実際の開発プロセスを体験しましょう。

> 📚 **参照**: この演習は GitHub Spec Kit の[公式ドキュメント](https://github.com/github/spec-kit)の基本フローに基づいています。

### 📝 演習の目標

**タスク:** チケット一覧画面からキーワード検索すると該当するタイトルのチケットを取得できる機能を実装する。

**達成基準:**
- ✅ プロジェクト憲法（constitution.md）が設定されている
- ✅ 仕様書（spec.md）が作成されている
- ✅ 技術設計（plan.md）が作成されている
- ✅ タスク分解（tasks.md）が作成されている
- ✅ コードが実装され、テストが成功している

---

### STEP 1: `/speckit.constitution` - プロジェクト原則の確立

**何をするか**: プロジェクト全体の開発規約・品質基準・技術方針を定義

**いつ実行するか**: プロジェクト開始時に1回だけ（既存プロジェクトに追加する場合も最初に実行）

#### :pen: 実行コマンド

Copilot Chat で以下を入力してください：

```text
/speckit.constitution

社内ヘルプデスクプロジェクト原則を作成してください。コード品質、テスト基準、ユーザー体験の一貫性、パフォーマンス要件に焦点を当てます。フロントエンド: Next.js 15 + React 19 + TypeScript (strict mode) + shadcn/ui。バックエンド: FastAPI + SQLAlchemy + PostgreSQL。テスト: フロントエンドは Vitest、バックエンドは pytest (80%以上のカバレッジ)、E2E は Playwright。パフォーマンス: API レスポンス < 200ms、フロントエンド読み込み < 1s。これらの原則が技術的な意思決定と実装の選択をどのように導くべきかのガバナンスを含めてください。
```

#### :robot: 期待される出力

**1. プロジェクト憲法の作成確認**

下記は、consitution.mdの一部である。

```markdown
# Internal Helpdesk Project Constitution

## Core Principles

### I. Type Safety First (NON-NEGOTIABLE)

**TypeScript strict mode** is mandatory for all frontend code. **Python type hints** are mandatory for all backend code.

- Frontend: `tsconfig.json` MUST have `"strict": true` enabled
- Backend: All functions and methods MUST have type annotations for parameters and return values
- Type violations MUST NOT pass code review or CI/CD pipeline
- Use of `any` in TypeScript or type ignores in Python requires explicit justification in code comments

**Rationale**: Type safety prevents runtime errors, improves IDE support, and serves as living documentation. In a multi-developer enterprise environment, type safety is critical for maintainability and reduces debugging time significantly.

### II. Test Coverage Standard (NON-NEGOTIABLE)

**Minimum 80% code coverage** is required for both frontend and backend.

- Frontend: Vitest for unit/integration tests, minimum 80% coverage
- Backend: pytest for unit/integration tests, minimum 80% coverage
- E2E: Playwright for critical user flows (authentication, ticket creation, article publishing)
- Coverage reports MUST be generated on every PR
- PRs that drop coverage below 80% MUST NOT be merged without explicit justification
- Test-first development is strongly encouraged: write tests before implementation when possible

**Rationale**: High test coverage ensures reliability in production, enables confident refactoring, and catches regressions early. In a helpdesk system where data integrity and user trust are paramount, comprehensive testing is non-negotiable.
```

**2. 更新されるファイル**

specify コマンドにより、以下のファイルが更新されます:

```
.specify/
├── memory/
│   └── constitution.md    # プロジェクト憲法
└── templates/
    ├── spec-template.md   # 仕様書テンプレート
    ├── plan-template.md   # 技術設計テンプレート
    └── tasks-template.md  # タスク分解テンプレート
```

---

### STEP 2: `/speckit.specify` - 機能仕様の作成

**何をするか**: 実装したい機能を自然言語で説明し、構造化された仕様書を生成

**重要**: この段階では**技術スタックには触れず**、「何を作るか（WHAT）」と「なぜ作るか（WHY）」に集中します。

#### :pen: 実行コマンド

```text
/speckit.specify

チケット検索機能を実装してください。サポートスタッフは多数のチケットの中から特定のチケットを素早く見つける必要があります。チケット一覧ページに検索ボックスを追加してください。キーワードを入力すると、タイトルでチケットをリアルタイムにフィルタリングします。検索キーワードを URL パラメータ (?search=keyword) に保存し、ページ再読み込み時に検索状態を維持します。検索が空の場合はすべてのチケットを表示します。一致するチケットがない場合は「検索結果が見つかりませんでした」というメッセージを表示してください。
```

#### :robot: 期待される動作

**1. 自動ブランチ作成**

```bash
$ .specify/scripts/bash/create-new-feature.sh --json "ticket search feature"
→ `001-ticket-search` ブランチに自動切り替え
```

**2. AI との対話的な仕様書作成**

AI が不明点や曖昧な部分を確認してきます：(確認をされないケースもあります。その場合は、speckit.clarify フェーズで質問してください)

```text
**AI アシスタント**

I'm creating the functional specification for the ticket search feature. I have a few clarifications:

1. Should the search be case-sensitive or case-insensitive?
2. Should we support partial matches (e.g., "log" matches "login")?
3. What should happen when there are no search results?
4. Should we show a loading indicator during search?

**あなたの応答例**:
```text
1. Case-insensitive（大文字小文字を区別しない）
2. Yes, partial matches should work（部分一致でOK）
3. Show "検索結果が見つかりませんでした" message
4. Yes, show loading indicator for better UX
```

**3. 仕様書の生成**

下記は生成された仕様書の一部ですが、機能要件が詳細に列挙されています：

````markdown
### Functional Requirements

- **FR-001**: システムは、チケット一覧ページに検索ボックスを表示しなければならない
- **FR-002**: システムは、検索ボックスに入力されたキーワードに基づいて、チケットのタイトルをリアルタイムにフィルタリングしなければならない
- **FR-003**: システムは、検索キーワードをURLパラメータ `?search=keyword` として保存しなければならない
- **FR-004**: システムは、URLパラメータ `?search=keyword` が存在する場合、ページ読み込み時にその値を検索ボックスに復元し、フィルタリングを適用しなければならない
- **FR-005**: システムは、検索キーワードが空の場合、すべてのチケットを表示しなければならない
- **FR-006**: システムは、検索結果が0件の場合、「検索結果が見つかりませんでした」というメッセージを表示しなければならない
- **FR-007**: システムは、検索機能を既存のステータスフィルタおよび優先度フィルタと組み合わせて使用できなければならない
- **FR-008**: システムは、検索キーワードの変更を debounce（300ms）で処理し、過度なAPIリクエストを防がなければならない
- **FR-009**: システムは、検索キーワードに特殊文字や日本語が含まれる場合も正しく処理しなければならない
- **FR-010**: システムは、検索が case-insensitive（大文字小文字を区別しない）で動作しなければならない

````

**4. 仕様書完成通知**

```markdown
✅ チケット検索機能仕様書作成完了
作成ファイル
spec.md - チケット検索機能の完全な仕様書
````

**生成されるディレクトリ構造**:

```
.specify/
├── memory/
│   └── constitution.md
├── specs/
│   └── 001-ticket-search/     # 新規作成
│       └── spec.md             # 機能仕様書
└── templates/
    ├── spec-template.md
    ├── plan-template.md
    └── tasks-template.md
```

---

### STEP 3: `/speckit.clarify` - 仕様の明確化（オプション）

**何をするか**: 仕様の曖昧な点や不明点を構造化された質問で解消

**いつ実行するか**: `/speckit.specify` の後、`/speckit.plan` の前（仕様に `NEEDS CLARIFICATION` がある場合）

#### :pen: 実行コマンド

```text
/speckit.clarify

仕様書の FR-007 と FR-008 について明確化してください。
- FR-007: 検索APIのレスポンスタイムは200ms以内とします（constitution に従う）
- FR-008: 検索結果が0件の場合は HTTP 200 を返し、空配列と「検索結果が見つかりませんでした」メッセージを返してください
- 検索キーワードは最大100文字までとします
- 検索中はローディング状態を表示してください
```

#### :robot: 期待される出力

**1. 明確化された要件の確認**

```markdown
✅ 仕様の明確化が完了しました

以下の要件が spec.md に追加されました:

**FR-007 (更新)**: システムは検索APIのレスポンスタイムを200ms以内に保たなければならない
**FR-008 (更新)**: システムは検索結果が0件の場合、HTTP 200ステータスコードと空配列、および「検索結果が見つかりませんでした」メッセージを返さなければならない
**FR-011 (新規)**: システムは検索キーワードの最大長を100文字に制限しなければならない
**FR-012 (新規)**: システムは検索中にローディングインジケーターを表示しなければならない

次のステップ: /speckit.plan を実行して技術設計を開始してください
```

**2. 更新されるファイル**

```
.specify/
└── specs/
    └── 001-ticket-search/
        └── spec.md    # 明確化された要件が追加される
```

> 💡 **Tips**: `/speckit.clarify` は省略可能ですが、仕様の曖昧さを事前に解消することで、後の実装フェーズでの手戻りを防げます。

---

### STEP 4: `/speckit.plan` - 技術設計

**何をするか**: 仕様書をもとに、技術的な実装方法を具体化し、詳細な設計ドキュメントを作成

**いつ実行するか**: `/speckit.specify`（または `/speckit.clarify`）の後、技術スタックを指定して実装計画を立てる段階

#### :pen: 実行コマンド

```text
/speckit.plan

チケット検索機能の技術設計を作成してください。フロントエンドは Next.js 15 + React 19 + TypeScript + shadcn/ui を使用。バックエンドは FastAPI + SQLAlchemy を使用。既存のプロジェクト構造に従って実装してください。
```

#### :robot: 期待される出力

**1. 技術設計書の概要提示**

```markdown
## 📋 実装計画の概要

### バックエンド (FastAPI + SQLAlchemy)
- エンドポイント: GET /api/tickets に search パラメータ追加
- 検索ロジック: SQLAlchemy LIKE検索
- 変更ファイル:
  - `app/routers/tickets.py` - search パラメータ追加
  - `app/services/ticket_service.py` - 検索ロジック追加
  - `tests/test_tickets.py` - テスト追加
- 見積もり: 約45分

### フロントエンド (Next.js 15 + shadcn/ui)
- UI: SearchBox コンポーネント作成
- 状態管理: URL Search Params 連携
- 変更ファイル:
  - `components/search-box.tsx` - SearchBox作成
  - `app/tickets/page.tsx` - SearchBox統合
  - `lib/api-client.ts` - 検索パラメータ追加
  - `__tests__/search-box.test.tsx` - テスト追加
- 見積もり: 約60分

### リスク
- 検索パフォーマンス → インデックス追加で対応
- デバウンス処理 → useDebounce hook 使用
```

**2. 技術設計書完成通知**

```markdown
✅ 技術設計書を作成しました！

作成場所: .specify/specs/001-ticket-search/

次のステップ: /speckit.tasks を実行してタスク分解を行ってください。
```

**3. 生成されるファイル構造**

```
.specify/
└── specs/
    └── 001-ticket-search/
        ├── contracts/              # API仕様とインターフェース定義
        │   ├── api-spec.json      # REST API仕様（OpenAPI形式）
        │   └── component-interfaces.md  # コンポーネント間インターフェース
        ├── data-model.md          # データモデル設計
        ├── plan.md                # 技術設計書（メイン）
        ├── quickstart.md          # 実装クイックスタートガイド
        ├── research.md            # 技術調査結果
        └── spec.md                # 機能仕様書（STEP 2で作成済み）
```

**各ファイルの内容:**

- **plan.md**: 実装計画の全体像（アーキテクチャ、ファイル構成、見積もり）
- **contracts/api-spec.json**: バックエンドAPIの詳細仕様（エンドポイント、リクエスト/レスポンス形式）
- **data-model.md**: データベーススキーマとモデル設計
- **research.md**: 使用技術（Next.js 15、FastAPI、shadcn/ui）の調査結果とベストプラクティス
- **quickstart.md**: 開発環境セットアップと実装手順の概要

---

### STEP 5: `/speckit.tasks` - タスク分解

**何をするか**: 技術設計をもとに、実装可能な小さなタスクに分解

**いつ実行するか**: `/speckit.plan` の後、`/speckit.implement` の前

#### :pen: 実行コマンド

```text
/speckit.tasks
```

#### :robot: 期待される出力

下記のようにタスクリスト集が生成されます。

```markdown
## フェーズ 1: セットアップ（共有インフラ）

**目的**: プロジェクトの初期化と検証

**注意**: バックエンドは既に検索機能をサポートしているため、バックエンドのセットアップは不要

- [ ] T001 バックエンド検索APIが `GET /api/tickets?search=test` でアクセス可能であることを確認（200が返されるべき）
- [ ] T002 [P] フロントエンドのTypeScript strict modeが `PoC-GitHubCopilotApp2-Frontend/tsconfig.json` で有効化されていることを確認
- [ ] T003 [P] Vitestが `PoC-GitHubCopilotApp2-Frontend/vitest.config.mts` で設定されていることを確認
- [ ] T004 [P] Playwrightが `PoC-GitHubCopilotApp2-Frontend/playwright.config.ts` で設定されていることを確認
- [ ] T005 [P] shadcn/ui Inputコンポーネントが `PoC-GitHubCopilotApp2-Frontend/components/ui/input.tsx` に存在することを確認

**チェックポイント**: 検索機能実装のための環境準備完了

---

## フェーズ 2: 基盤構築（必須の前提条件）

**目的**: すべてのユーザーストーリーを実装する前に完了させる必要があるコアインフラ

**⚠️ 重要**: このフェーズが完了するまで、ユーザーストーリーの作業を開始できません

- [ ] T006 `PoC-GitHubCopilotApp2-Frontend/lib/hooks/useDebounce.ts` にカスタムデバウンスフックを作成（TypeScriptジェネリクスサポート、デフォルト300ms遅延）
- [ ] T007 `PoC-GitHubCopilotApp2-Frontend/__tests__/hooks/useDebounce.test.ts` にuseDebounceフックの単体テストを作成（即時値、遅延後のデバウンス値、アンマウント時のクリーンアップをテスト）
- [ ] T008 `PoC-GitHubCopilotApp2-Frontend/lib/api-client.ts` のfetchTickets関数を更新し、オプショナルな `search?: string` パラメータを受け入れ、クエリ文字列に含めるようにする
- [ ] T009 `PoC-GitHubCopilotApp2-Frontend/lib/types.ts` を更新し、既存のTicketsParamsインターフェースに `search?: string` を追加（まだ存在しない場合）

**チェックポイント**: 基盤準備完了 - ユーザーストーリーの実装を並行して開始可能
```

**2. 生成されるファイル**

```
.specify/
└── specs/
    └── 001-ticket-search/
        ├── spec.md      # 機能仕様書
        ├── plan.md      # 技術設計書
        └── tasks.md     # タスク分解 (新規作成)
```

> 💡 **Tips**: タスク分解により、プルリクエストのチェックリストとして活用できます。各タスクを順次実装していくことで、進捗が可視化されます。

---

### STEP 6: `/speckit.implement` - 実装

#### :pen: 実行コマンド

```text
/speckit.implement
```

#### :robot: 期待される動作

タスクリストに基づいて、必要な実装を行います。

---

### STEP 7: `/speckit.analyze` - 一貫性チェック（オプション）

**何をするか**: 仕様書、技術設計、タスク分解の一貫性を検証

**いつ実行するか**: `/speckit.implement` の前（実装開始前の最終確認）

#### :pen: 実行コマンド

```text
/speckit.analyze

spec.md、plan.md、tasks.md の一貫性を分析してください。仕様の漏れ、技術設計の矛盾、タスクの不足がないかチェックしてください。
```

#### :robot: 期待される出力

**1. 一貫性チェック結果**

```markdown
✅ 一貫性チェック完了

## 分析結果

### ✅ 仕様カバレッジ
- spec.md の全12個の機能要件が plan.md でカバーされています
- plan.md の全ての設計要素が tasks.md に含まれています

### ✅ 技術的整合性
- フロントエンドとバックエンドのインターフェースが一致しています
- URL パラメータ形式が両方で統一されています (?search=keyword)
- エラーハンドリング方針が一貫しています

### ⚠️ 軽微な推奨事項
1. **パフォーマンス**: データベースインデックスの追加を検討してください
   - tickets テーブルの title カラムにインデックスを追加すると検索が高速化されます
   
2. **セキュリティ**: SQLインジェクション対策を明示してください
   - SQLAlchemy の ilike は自動的にエスケープしますが、plan.md に明記すると良いでしょう

### ✅ テストカバレッジ
- 単体テスト、統合テスト、E2Eテストが適切に計画されています
- 80%以上のカバレッジ目標を達成できる見込みです

## 結論
仕様、設計、タスクは一貫しており、実装開始可能です。
推奨事項は実装後に対応することも可能です。

次のステップ: /speckit.implement を実行して実装を開始してください
```

> 💡 **Tips**: `/speckit.analyze` は省略可能ですが、大規模な機能や複数人での開発時には、実装前の最終チェックとして有効です。

---

## 補足: その他の便利なコマンド

### `/speckit.checklist` - 品質チェックリスト生成

仕様書の品質を検証するチェックリストを生成します。

```text
/speckit.checklist

spec.md の品質チェックリストを生成してください。要件の完全性、明確性、一貫性を検証してください。
```

**期待される出力:**

```markdown
## 仕様書品質チェックリスト

### 完全性チェック ✅
- [x] すべてのユーザーストーリーが機能要件に変換されている
- [x] 非機能要件（パフォーマンス、セキュリティ）が定義されている
- [x] エラーケースと境界値が明示されている

### 明確性チェック ✅
- [x] 曖昧な表現 (NEEDS CLARIFICATION) が解消されている
- [x] 受け入れ基準が測定可能である
- [x] 技術用語が一貫して使用されている

### 一貫性チェック ⚠️
- [x] プロジェクト憲法との整合性がある
- [ ] 他の機能仕様との重複がない (要確認)

### テスタビリティチェック ✅
- [x] 各要件がテスト可能である
- [x] 受け入れ基準が明確である
```

---

## 実践演習の完全フロー まとめ

チケット検索機能の実装では、以下の7ステップを実行しました：

```
STEP 1: /speckit.constitution
  ↓ プロジェクト憲法を設定（初回のみ）
  
STEP 2: /speckit.specify
  ↓ 機能仕様を作成（自動ブランチ作成）
  
STEP 3: /speckit.clarify (オプション)
  ↓ 仕様の曖昧さを解消
  
STEP 4: /speckit.plan
  ↓ 技術設計を作成
  
STEP 5: /speckit.tasks
  ↓ 実装タスクに分解
  
STEP 6: /speckit.implement
  ↓ コードを実装・テスト
  
STEP 7: /speckit.analyze (オプション)
  ↓ 一貫性を最終確認
  
✅ 実装完了・PR作成
```

**最小限のフロー**（シンプルな機能の場合）:
```
constitution → specify → plan → implement
```

**推奨フロー**（実務・チーム開発の場合）:
```
constitution → specify → clarify → plan → tasks → analyze → implement
```
---

## :memo: 練習

以下の練習で Spec Kit の理解を深めましょう：

1. **基本ワークフローの実行**
   - 新しいプロジェクトで Spec Kit を初期化してください
   - `/speckit.constitution` でプロジェクト憲法を設定してください
   - シンプルな API エンドポイント（例: GET /ping）を `/speckit.specify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.implement` で実装してください
   - 生成されたコードとテストを確認してください

2. **実務プロジェクトへの適用**
   - 自分のプロジェクトに Spec Kit を導入してください
   - プロジェクト固有の開発規約を `/speckit.constitution` で定義してください
   - 小さな機能から始めて、徐々に複雑な機能に適用してください

> Spec Kit は公式ツールのため、GitHub の標準的なベストプラクティスが反映されています。チーム開発で標準化された開発プロセスを確立したい場合に特に有効です。

---

## まとめ

* **Spec Kit** は GitHub が提供する仕様駆動開発の公式ツールキット
* **仕様ファースト** により、AI のオーバーエンジニアリングを防止し、期待通りの実装を実現
* **8つのコマンド** で開発プロセスを構造化（5つのコアコマンド + 3つのオプショナルコマンド）
* **自動ブランチ管理** により、1機能 = 1ブランチ = 1PR の構造を自動実現
* **マルチエージェント対応** により、GitHub Copilot 以外の AI でも同じワークフローを実行可能
* **`/speckit.constitution` で統制** することで、プロジェクト全体の一貫性を保ちながら開発を進められる

Spec Kit は、特に以下のような場面で威力を発揮します：

- **チーム開発**: 標準化されたプロセスでチーム全体の品質を均一化
- **複数 AI の活用**: GitHub Copilot、Claude、Cursor を使い分けたい場合
- **エンタープライズ開発**: 仕様書ベースの厳格な開発プロセスが必要な場合
- **複雑なプロジェクト**: 多数の機能を並行開発し、PR を管理したい場合

Spec Kit を活用して、AI コーディングの恩恵を最大限に引き出し、効率的かつ高品質なソフトウェア開発を実現しましょう。