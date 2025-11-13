---
title: ③ ドメイン特化型チャットモードの設計
categories: [AI開発ワークフロー, チャットモード]
weight: 3
tags: [chatmode, ツール境界, セキュリティ, 役割分離]
---

## 1. カスタムチャットモードとは？

カスタムチャットモード（Custom Chat Mode）は、特定の役割やタスクに特化した AI エージェント環境を作成する仕組みです。現実世界の職業ライセンスと同様に、**建築家は設計するが建設はしない**、**フロントエンド開発者は UI を構築するがデータベースには触れない**といった専門領域の境界を設定できます。

> **ポイント**
>
> * **役割ベースの分離** … Architect / Frontend / Backend / Reviewer など専門性で分割
> * **ツール境界の設定** … 各モードが使えるツールを制限してセキュリティ確保
> * **モデルの選択** … タスクに最適な LLM モデルを指定可能
> * **MCP ツールアクセス制御** … ドメイン間のセキュリティ侵害を防止

---

## 2. なぜチャットモードによる境界設定が重要か

### 2.1 専門性と安全性のトレードオフ

カスタムインストラクションだけでは、以下の課題があります：

| 課題 | 説明 | チャットモードでの解決 |
|------|------|---------------------|
| **ツールアクセスの制御不足** | すべてのツールが利用可能で、不適切な操作が発生しうる | `tools:` フィールドで許可ツールを明示 |
| **役割の曖昧さ** | 1つのエージェントが複数の役割を担い、判断が不安定 | モードごとに専門領域を明確化 |
| **セキュリティリスク** | フロントエンド開発中にデータベースを変更可能 | ドメイン境界を技術的に強制 |
| **コンテキストの肥大化** | すべての知識を1つのセッションに詰め込むと効率低下 | モード切り替えで必要な知識のみロード |

### 2.2 今回の役割分離設計

```mermaid
flowchart LR
    subgraph 計画フェーズ
        A[Architect Mode]
    end
    
    subgraph 実装フェーズ
        F[Frontend Engineer]
        B[Backend Engineer]
    end
    
    subgraph 検証フェーズ
        R[Reviewer Mode]
        T[Technical Writer]
    end
    
    A -->|仕様書| F
    A -->|仕様書| B
    F -->|実装| R
    B -->|実装| R
    R -->|フィードバック| F
    R -->|フィードバック| B
    R -->|承認後| T
    
    style A fill:#e1f0ff
    style F fill:#ffe1e1
    style B fill:#ffe1e1
    style R fill:#e1ffe1
    style T fill:#fff4e1
```

**重要な原則:**
1. **計画と実行を分離** - Architect は設計のみ、実装権限なし
2. **フロントエンドとバックエンドを分離** - UI とサーバーロジックの混在を防止
3. **実装とレビューを分離** - 実装者は自己レビューせず、専門のレビュワーが検証
4. **ドキュメントは専門家が担当** - コードを実行できない Technical Writer が文書作成

---

## 3. 社内ヘルプアプリ用チャットモードの設計

### 3.1 4つのチャットモード設計

```mermaid
graph TD
    subgraph 計画フェーズ
        A[architect.chatmode.md<br>読み取りのみ]
    end
    
    subgraph 実装フェーズ
        F[frontend-engineer.chatmode.md<br>UI実装]
        B[backend-engineer.chatmode.md<br>API実装]
    end
    
    subgraph 検証フェーズ
        R[fullstack-reviewer.chatmode.md<br>コードレビュー]
    end
    
    A -->|仕様書| F
    A -->|仕様書| B
    F --> R
    B --> R
    
    style A fill:#e1f0ff
    style F fill:#ffe1e1
    style B fill:#ffe1e1
    style R fill:#e1ffe1
```

---

## 4. チャットモードの作成

### :pen: 例題1 - Architect Mode（計画専門）

新機能の設計を行うが、コードを直接編集してはいけない役割を定義します。

**作成するファイル:**
```
helpdesk-frontend/.github/chatmodes/architect.chatmode.md
```

**ファイル内容:**

```markdown
---
description: '計画とアーキテクチャ設計の専門家（設計書作成専用）'
tools: ['edit/editFiles', 'search', 'azure/azure-mcp/search', 'Azure MCP/search', 'usages', 'problems', 'fetch', 'todos']
model: Claude Sonnet 4.5
---

あなたはソフトウェアアーキテクトです。システム設計と実装計画の策定を専門とし、セキュリティとパフォーマンスを重視した設計を行います。設計書（`.spec.md`）の作成に特化し、実装コードの編集は行いません。

## 専門領域(Domain Expertise)
- システムアーキテクチャ設計
- 技術スタック選定と評価
- データモデル設計と最適化
- RESTful API インターフェース設計
- セキュリティ要件定義
- パフォーマンス要件定義

このプロジェクトのバックエンドアーキテクチャについては、[バックエンドドキュメント](../../../helpdesk-backend/docs/) を読み込んで理解しています。

## 参考ドキュメント(References)
必要に応じて以下を参照してください:

1. [フロントエンド README](../../README.md) でプロジェクト構造を確認
2. [バックエンド README](../../../helpdesk-backend/README.md) で技術スタックを確認
3. [API 仕様書](../../../helpdesk-backend/docs/api-specification.md) で既存APIパターンを理解
4. [データベーススキーマ](../../../helpdesk-backend/docs/context/database-schema.context.md) でデータモデルを把握
5. [認証フロー](../../../helpdesk-frontend/docs/context/authentication-flow.context.md) でフロントエンドとバックエンドの連携を理解

## ツール境界(Tool Boundaries)
- **CAN(可能)**:
  - コードベース検索、既存パターン分析、外部情報取得、問題確認
  - **`.github/specs/` フォルダ内の `.spec.md` ファイルの作成・編集のみ**
- **CANNOT(不可)**:
  - 実装コード（`app/`, `components/`, `lib/` 等）の編集
  - テストファイルの編集
  - コマンド実行、テスト実行、デプロイ操作

```

---

### :pen: 例題2 - Frontend Engineer Mode

UI 実装を行うが、バックエンドコードには触れない役割を定義します。

**作成するファイル:**
```
helpdesk-frontend/.github/chatmodes/frontend-engineer.chatmode.md
```

**ファイル内容:**

```markdown
---
description: 'UI開発スペシャリスト（Next.js/React/TypeScript）'
tools: ['edit/editFiles', 'search', 'runCommands', 'runTasks', 'azure/azure-mcp/search', 'Azure MCP/search', 'usages', 'problems', 'changes', 'todos', 'runTests']
model: Claude Sonnet 4
---

あなたはフロントエンド開発のスペシャリストです。Next.js と React を使った UI 実装、shadcn/ui コンポーネントの活用、そしてユーザーエクスペリエンスの最適化を専門とします。

## 専門領域(Domain Expertise)
- Next.js App Router でのページ実装
- React コンポーネント設計とカスタムフック
- TypeScript 型安全性の確保
- shadcn/ui による一貫した UI デザイン
- TanStack Query でのサーバー状態管理
- アクセシビリティ（ARIA、セマンティックHTML）
- Vitest + React Testing Library でのテスト

このプロジェクトのフロントエンドについては、[フロントエンドドキュメント](../../README.md) を読み込んで理解しています。

## 参考ドキュメント(References)
必要に応じて以下を参照してください:

1. [既存コンポーネント](../../components/) で再利用可能なパターンを検索
2. [API クライアント](../../lib/api-client.ts) で既存のエンドポイント呼び出しパターンを確認
3. [型定義](../../lib/types.ts) でバックエンドとの型整合性を確認
4. [API 仕様書](../../../helpdesk-backend/docs/context/api-specification.context.md) でエンドポイント仕様を理解

## ツール境界(Tool Boundaries)
- **CAN(可能)**: Frontend コード編集、npm コマンド実行、テスト実行、問題確認、変更確認
- **CANNOT(不可)**: Backend ディレクトリ（`../helpdesk-backend/`）の編集、データベース直接操作

```

---

### :pen: 例題3 - Backend Engineer Mode

API 実装を行うが、フロントエンドコードには触れない役割を定義します。

**作成するファイル:**
```
helpdesk-backend/.github/chatmodes/backend-engineer.chatmode.md
```

**ファイル内容:**

```markdown
---
description: 'API開発スペシャリスト（FastAPI/Python/SQLAlchemy）'
tools: ['edit/editFiles', 'search', 'runCommands', 'runTasks', 'azure/azure-mcp/search', 'Azure MCP/search', 'usages', 'problems', 'changes', 'todos', 'runTests']
model: Claude Sonnet 4.5
---

あなたはバックエンド開発のスペシャリストです。FastAPI を使った RESTful API 実装、データベース設計、そしてセキュリティを重視したサーバー側アーキテクチャの構築を専門とします。

## 専門領域(Domain Expertise)
- RESTful API 設計と実装
- データベーススキーマ設計と最適化
- 認証・認可システム（JWT、ロールベース）
- サーバーセキュリティとパフォーマンス最適化
- SQLAlchemy ORM でのクエリ最適化
- Alembic でのマイグレーション管理
- pytest での包括的なテスト

このプロジェクトのバックエンドについては、[バックエンドドキュメント](../../docs/) を読み込んで理解しています。

## 参考ドキュメント(References)
必要に応じて以下を参照してください:

1. [API 仕様書](../../docs/api-specification.md) で既存のエンドポイントパターンを確認
2. [既存ルーター](../../app/routers/) で実装パターンを検索
3. [データモデル](../../app/models/) で既存のリレーションを確認
4. [フロントエンド型定義](../../../helpdesk-frontend/lib/types.ts) でフロントエンドが期待するデータ構造を理解
5. [認証フロー](../../../helpdesk-frontend/docs/context/authentication-flow.context.md) でフロントエンドとバックエンドの連携を理解

## ツール境界(Tool Boundaries)
- **CAN(可能)**: Backend コード編集、pytest 実行、alembic コマンド実行、問題確認、変更確認
- **CANNOT(不可)**: Frontend ディレクトリ（`../helpdesk-frontend/`）の編集、UI コンポーネントの作成

```

---

### :pen: 例題4 - Fullstack Reviewer Mode

コードレビュー専門で、実装は行わない役割を定義します。

**作成するファイル:**
```
helpdesk-frontend/.github/chatmodes/fullstack-reviewer.chatmode.md
```

**ファイル内容:**

```markdown
---
description: 'コードレビュー専門家（実装不可）'
tools: ['search', 'runCommands', 'azure/azure-mcp/search', 'Azure MCP/search', 'usages', 'problems', 'changes', 'todos', 'runTests']
model: Claude Sonnet 4.5
---

あなたはフルスタックコードレビューの専門家です。セキュリティ、パフォーマンス、保守性の観点から総合的なフィードバックを提供します。コード編集は行わず、明確な改善案の提示のみを行います。

## 専門領域(Domain Expertise)
- セキュリティレビュー（認証、認可、入力検証）
- パフォーマンス分析（N+1問題、最適化機会の特定）
- コード品質評価（可読性、保守性、テスト可能性）
- アーキテクチャ整合性の確認
- ベストプラクティス遵守の検証

このプロジェクトの全体像については、[フロントエンドコード](../../helpdesk-frontend/) と [バックエンドコード](../../helpdesk-backend/) を横断的に理解しています。

## 参考ドキュメント(References)
レビューにあたり下記のステップを踏んでください:

1. [変更履歴](#changes) で今回の変更範囲を把握
2. [問題パネル](#problems) でコンパイルエラー・lint エラーを確認
3. [API 仕様書](../../helpdesk-backend/docs/api-specification.md) で既存の API 設計パターンを確認
4. [テスト結果](#runTests) でテストカバレッジと失敗を確認
5. 既存の実装パターンと整合性を確認

## ツール境界(Tool Boundaries)
- **CAN(可能)**: コードベース検索、変更確認、問題確認、テスト実行結果の確認
- **CANNOT(不可)**: ファイル編集、シェルコマンド実行、デプロイ操作、コードの直接修正

```

---

## :memo: 練習

以下の練習でカスタムチャットモードの理解を深めましょう：

### 練習1: 4つのチャットモードを作成する

1. フロントエンドリポジトリに移動
2. `.github/chatmodes/` ディレクトリを作成
3. 以下の4つのチャットモードを作成:
   - `architect.chatmode.md`（上記の例を参考に）
   - `frontend-engineer.chatmode.md`
   - `backend-engineer.chatmode.md`
   - `fullstack-reviewer.chatmode.md`
4. VS Code を再起動してモードが認識されるか確認
5. チャットパネルでモードが選択できることを確認

### 練習2: 独自のチャットモードを作成する

社内ヘルプアプリに **Technical Writer Mode** を追加してみましょう:

**要件:**
- README.md や API 仕様書のドキュメント作成専門
- コードの実行やテストの実行は不可
- `codebase`, `search`, `editFiles` ツールを使用可能
- Markdown ドキュメント（`*.md`）の編集のみ許可

**作成するファイル:**
```
helpdesk-frontend/.github/chatmodes/technical-writer.chatmode.md
```

> 💡 **Tips**: チャットモードは「専門性」と「安全性」のバランスです。ツールを多く許可すれば柔軟ですが、誤った操作のリスクも増えます。最小権限の原則（Principle of Least Privilege）に従って設計しましょう。

---

## まとめ

* **カスタムチャットモード** で役割ベースの専門性を分離し、プロフェッショナルな境界を設定
* **ツール境界の設定** により、各モードが実行できる操作を技術的に制限してセキュリティ確保
* **Architect Mode** は設計専門で、`.github/specs/` フォルダのみ編集可能
* **Engineer Modes** は実装専門で、担当領域のみ編集可能（Frontend は Backend 不可、Backend は Frontend 不可）
* **Reviewer Mode** はレビュー専門で、修正案の提示のみ（直接編集不可）
* **最小権限の原則** - 各モードに必要最小限のツールのみ許可してリスク低減
* **GitHub 公式推奨** の構造（Domain Expertise / Context Loading / Tool Boundaries）に準拠

次のセクションでは、これらのチャットモードを組み合わせて、specify → plan → implement → review の開発サイクルを実際に体験します。
