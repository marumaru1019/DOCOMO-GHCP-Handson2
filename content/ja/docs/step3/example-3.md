---
title: ③ カスタムインストラクションの作成
categories: [AI開発ワークフロー, カスタマイズ]
weight: 3
tags: [copilot-instructions, applyTo, プロジェクト固有ルール]
---

## 1. カスタムインストラクションとは？

カスタムインストラクション（Custom Instructions）は、GitHub Copilot にプロジェクト固有のルールやベストプラクティスを教えるための仕組みです。新しいチームメンバーに「このプロジェクトの決まり事」を説明するように、Copilot にもプロジェクトの文脈を伝えることで、より正確で一貫性のある提案が得られます。

**2種類のインストラクション:**
- **カスタムインストラクション** (`.github/copilot-instructions.md`) - リポジトリ全体に適用される共通ルール
- **パス別インストラクション** (`.github/instructions/*.instructions.md`) - 特定のファイルパターンに適用される専門的なルール


---

## 2. カスタムインストラクションの構造

### 2.1 マルチワークスペースでのインストラクション配置

マルチリポジトリ構成では、各リポジトリに `.github/copilot-instructions.md` を配置します。

```
Workspace/（ワークスペースルート）
├── helpdesk-frontend/（社内ヘルプアプリ - フロントエンド）
│   ├── .github/
│   │   ├── copilot-instructions.md（カスタムインストラクション）
│   │   └── instructions/
│   │       ├── frontend.instructions.md（applyTo: "**/*.{ts,tsx}"）
│   │       └── testing.instructions.md（applyTo: "**/*.test.{ts,tsx}"）
│   ├── components/
│   ├── lib/
│   └── app/
└── helpdesk-backend/（社内ヘルプアプリ - バックエンド）
    ├── .github/
    │   ├── copilot-instructions.md（カスタムインストラクション）
    │   └── instructions/
    │       ├── backend.instructions.md（applyTo: "**/*.py"）
    │       └── testing.instructions.md（applyTo: "**/test_*.py"）
    ├── app/
    └── tests/
```

### 2.2 インストラクションファイルの適用範囲

| ファイル | 分類 | 適用範囲 | 使用場面 |
|---------|------|---------|----------|
| **copilot-instructions.md** | カスタムインストラクション | 各リポジトリ全体 | プロジェクト概要、共通ルール |
| **frontend.instructions.md** | パス別インストラクション | `**/*.{ts,tsx}` | React/Next.js固有のルール |
| **backend.instructions.md** | パス別インストラクション | `**/*.py` | FastAPI/Python固有のルール |
| **testing.instructions.md** | パス別インストラクション | `**/*.test.{ts,tsx}` または `**/test_*.py` | テスト記述のルール |

---

## 3. カスタムインストラクションの作成

### :pen: 例題1 - フロントエンド用カスタムインストラクション

社内ヘルプアプリのフロントエンド用カスタムインストラクションを作成します。

**作成するファイル:** `helpdesk-frontend/.github/copilot-instructions.md`

### :robot: 推奨される5つのセクション
全体に適用させるカスタムインストラクションでは、以下の5つのセクションを含めることを推奨します：

1. **プロジェクト概要** - What（何を作っているか）
2. **技術スタック** - What（何を使っているか）
3. **コーディングガイドライン** - How（どう書くべきか）
4. **プロジェクト構造** - Where（どこに何があるか）
5. **利用可能なリソース** - Resources（どんなツールが使えるか）

**実装例:**

````markdown
# 社内ヘルプアプリ - フロントエンド

チケット管理システムのフロントエンドアプリケーション。企業内のサポートチケット、ナレッジベース、ユーザー管理を提供する内部ツールです。バックエンドAPI（FastAPI）と連携し、認証、チケット管理、ナレッジ記事の表示・編集機能を実装しています。

## 技術スタック

### フロントエンド
- **Next.js 15** (App Router) - ページルーティングとSSR
- **React 19** - UIコンポーネント
- **TypeScript** - 型安全性の確保
- **Tailwind CSS** - スタイリング
- **shadcn/ui** - UIコンポーネントライブラリ

### 状態管理・データフェッチ
- **Context API** - 認証状態管理（`lib/auth-context.tsx`）
- **fetch API** - APIクライアント（`lib/api-client.ts`）

### テスト
- **Vitest** - 単体テスト
- **React Testing Library** - コンポーネントテスト
- **Playwright** - E2Eテスト

### バックエンド連携
- **FastAPI バックエンド** - `../helpdesk-backend`
- **REST API** - JSONベースのデータ通信
- **JWT認証** - トークンベースの認証

## コーディングガイドライン

### 全般的な原則
- **一貫性**: プロジェクト全体で統一されたコーディングスタイルを維持
- **可読性**: 将来の自分や他の開発者が理解しやすいコードを書く
- **保守性**: 変更や拡張が容易な構造を心がける

### コミットとドキュメント
- コミットメッセージは変更内容を明確に記述
- 複雑なロジックにはコメントを追加
- READMEやドキュメントは常に最新の状態を保つ

### セキュリティとパフォーマンス
- 機密情報（APIキー、パスワード等）をコードに含めない
- パフォーマンスに影響する処理は最適化を検討
- ユーザー入力は必ずバリデーションを行う

### テストとデプロイ
- 新機能には必ずテストを追加
- 本番環境へのデプロイ前にローカルとステージング環境で動作確認
- CI/CDパイプラインを活用して品質を担保

## プロジェクト構造

- `app/` - Next.js App Router のページとレイアウト
  - `login/` - ログインページ
  - `tickets/` - チケット管理画面
  - `knowledge/` - ナレッジベース画面
  - `admin/` - 管理画面
- `components/` - 再利用可能なReactコンポーネント
  - `ui/` - shadcn/uiコンポーネント（Button, Card, Input等）
  - `header.tsx` - ナビゲーションヘッダー
  - `protected-route.tsx` - 認証ガード
- `lib/` - ユーティリティ関数と型定義
  - `types.ts` - TypeScript型定義（User, Ticket, Article等）
  - `api-client.ts` - APIクライアント
  - `auth-context.tsx` - 認証コンテキスト
  - `utils.ts` - 汎用ユーティリティ
- `__tests__/` - 単体テスト
- `e2e/` - E2Eテスト（Playwright）
- `mocks/` - テスト用のモックデータとハンドラ

## 利用可能なリソース

### スクリプト
- `npm run dev` - 開発サーバー起動
- `npm run build` - プロダクションビルド
- `npm test` - 単体テスト実行
- `npm run test:e2e` - E2Eテスト実行
- `npm run lint` - ESLint実行

### 重要な設定ファイル
- `tsconfig.json` - TypeScript設定
- `next.config.ts` - Next.js設定
- `tailwind.config.ts` - Tailwind CSS設定
- `playwright.config.ts` - Playwright設定

### バックエンドAPI仕様
バックエンドAPI仕様は `#file:../helpdesk-backend/docs/api-specification.md` を参照してください。

### 参考ドキュメント
- `README.md` - プロジェクトセットアップ手順
- `README-SETUP.md` - 環境構築の詳細手順
````

### :pen: 例題2 - バックエンド用カスタムインストラクション

社内ヘルプアプリのバックエンド用カスタムインストラクションを作成します。

**作成するファイル:** `helpdesk-backend/.github/copilot-instructions.md`

**実装例:**

````markdown
# 社内ヘルプアプリ - バックエンド

チケット管理システムのバックエンドAPI。企業内のサポートチケット、ナレッジベース、ユーザー管理を提供するREST APIです。FastAPIを使用し、JWT認証、PostgreSQLデータベース、Alembicマイグレーションを実装しています。

## 技術スタック

### バックエンドフレームワーク
- **FastAPI** - 高速なWeb APIフレームワーク
- **Python 3.11+** - プログラミング言語
- **Pydantic** - データバリデーションとスキーマ定義
- **SQLAlchemy 2.0** - ORMとデータベース操作

### データベース
- **PostgreSQL** - メインデータベース
- **Alembic** - データベースマイグレーションツール

### 認証・セキュリティ
- **JWT (JSON Web Tokens)** - トークンベース認証
- **bcrypt** - パスワードハッシュ化
- **python-jose** - JWTエンコード/デコード

### テスト
- **pytest** - テストフレームワーク
- **pytest-asyncio** - 非同期テスト
- **httpx** - HTTPクライアント（テスト用）

### フロントエンド連携
- **Next.js フロントエンド** - `../helpdesk-frontend`
- **REST API** - JSONベースのデータ通信
- **CORS** - クロスオリジンリクエスト対応

## コーディングガイドライン

### 全般的な原則
- **一貫性**: プロジェクト全体で統一されたコーディングスタイルを維持
- **可読性**: 将来の自分や他の開発者が理解しやすいコードを書く
- **保守性**: 変更や拡張が容易な構造を心がける

### コミットとドキュメント
- コミットメッセージは変更内容を明確に記述
- 複雑なロジックにはコメントを追加
- READMEやドキュメントは常に最新の状態を保つ

### セキュリティとパフォーマンス
- 機密情報（APIキー、パスワード等）をコードに含めない
- パフォーマンスに影響する処理は最適化を検討
- ユーザー入力は必ずバリデーションを行う

### テストとデプロイ
- 新機能には必ずテストを追加
- 本番環境へのデプロイ前にローカルとステージング環境で動作確認
- CI/CDパイプラインを活用して品質を担保

## プロジェクト構造

- `app/` - アプリケーションのメインコード
  - `main.py` - FastAPIアプリケーションのエントリーポイント
  - `routers/` - APIエンドポイント定義
    - `auth.py` - 認証関連エンドポイント
    - `tickets.py` - チケット管理エンドポイント
    - `articles.py` - ナレッジ記事エンドポイント
    - `admin.py` - 管理機能エンドポイント
  - `models/` - SQLAlchemyモデル定義
  - `schemas/` - Pydanticスキーマ定義
  - `services/` - ビジネスロジック
  - `core/` - コア機能（認証、設定、依存性注入）
  - `db/` - データベース接続設定
- `alembic/` - データベースマイグレーション
- `tests/` - pytestテストコード
- `docs/` - API仕様書とドキュメント

## 利用可能なリソース

### スクリプト
- `uvicorn app.main:app --reload` - 開発サーバー起動
- `pytest` - テスト実行
- `alembic upgrade head` - マイグレーション適用
- `alembic revision --autogenerate -m "message"` - マイグレーション生成

### 重要な設定ファイル
- `requirements.txt` - Python依存関係
- `alembic.ini` - Alembic設定
- `pytest.ini` - pytest設定

### API仕様書
API仕様の詳細は `#file:docs/api-specification.md` を参照してください。

### フロントエンド型定義
フロントエンドのTypeScript型定義は `#file:../helpdesk-frontend/lib/types.ts` を参照してください。
````

> 💡 **Tips**: バックエンド用インストラクションでは、フロントエンドの型定義ファイルを参照することで、API レスポンスとフロントエンドの型の一貫性を保ちやすくなります。

---

## 4. パス別インストラクションの作成

### 4.1 applyTo パターンによる選択的適用

パス別インストラクションは、`applyTo` フィールドで特定のファイルパターンにのみ適用されるルールを定義できます。これにより、コンテキストを最適化し、関連性の高いルールだけを Copilot に読み込ませます。

**GitHub 公式のベストプラクティス:**
- カスタムインストラクション（`copilot-instructions.md`）は全体の共通ルールのみ記載
- 技術スタック固有のルールは `.instructions.md` ファイルで分離
- `applyTo` パターンでファイルタイプごとにルールを適用

### :pen: 例題3 - フロントエンド用パス別インストラクション

**作成するファイル:** `helpdesk-frontend/.github/instructions/frontend.instructions.md`

````markdown
---
applyTo: "**/*.{ts,tsx}"
description: "TypeScript/React開発ガイドライン"
---

# TypeScript/React 開発ガイドライン

## Context Loading
実装前に以下を確認してください:
- [既存コンポーネントパターン](../../components/header.tsx)
- [型定義](../../lib/types.ts)

## Deterministic Requirements
- Server Components をデフォルトとし、必要な場合のみ `'use client'` を使用
- バックエンドのPydanticモデルに対応する型を `lib/types.ts` で定義
- shadcn/ui コンポーネント（Button, Card, Badge等）を優先的に使用
- `any` 型は避け、適切な型定義を使用

## Structured Output
コード生成時に以下を含める:
- [ ] インターフェース定義（Props, State等）
- [ ] エラーハンドリング
- [ ] 単体テスト（`__tests__/` ディレクトリ）
- [ ] アクセシビリティ対応（ARIA属性、セマンティックHTML）
````

### :pen: 例題4 - バックエンド用パス別インストラクション

**作成するファイル:** `helpdesk-backend/.github/instructions/backend.instructions.md`

````markdown
---
applyTo: "**/*.py"
description: "Python/FastAPI開発ガイドライン"
---

# Python/FastAPI 開発ガイドライン

## Context Loading
実装前に以下を確認してください:
- [APIルーターパターン](../../app/routers/)

## Deterministic Requirements
- PEP 8 に準拠し、型ヒント（Type Hints）を必ず使用
- Pydantic スキーマでリクエスト/レスポンスを定義
- フロントエンドのTypeScript型（`lib/types.ts`）と一致させる
- JWT認証とSQLAlchemy ORMを使用

## Structured Output
コード生成時に以下を含める:
- [ ] Docstring（Args, Returns, Raises）
- [ ] FastAPI依存性注入（`Depends`）
- [ ] pytest テストケース（`tests/test_*.py`）
- [ ] Alembicマイグレーション（スキーマ変更時）
````

### 4.2 テスト用パス別インストラクション

### :pen: 例題5 - フロントエンドテスト用パス別インストラクション

**作成するファイル:** `helpdesk-frontend/.github/instructions/testing.instructions.md`

````markdown
---
applyTo: "**/*.test.{ts,tsx}"
description: "React Testing Library テストガイドライン"
---

# フロントエンドテストガイドライン

## Context Loading
テスト作成前に以下を確認:
- [テストユーティリティ](../../__tests__/test-utils.tsx)
- [モックハンドラ](../../mocks/handlers/)

## Deterministic Requirements
- AAAパターン（Arrange, Act, Assert）に従う
- React Testing Library の推奨プラクティスを使用
- MSW（Mock Service Worker）でAPIモックを作成

## Structured Output
テストコード生成時に以下を含める:
- [ ] describe/it ブロックでテストを構造化
- [ ] ユーザー操作のシミュレーション（fireEvent, userEvent）
- [ ] アクセシビリティロール（getByRole）での要素取得
- [ ] エラーケースのテスト
````

### :pen: 例題6 - バックエンドテスト用パス別インストラクション

**作成するファイル:** `helpdesk-backend/.github/instructions/testing.instructions.md`

````markdown
---
applyTo: "**/test_*.py"
description: "pytest テストガイドライン"
---

# バックエンドテストガイドライン

## Context Loading
テスト作成前に以下を確認:
- [テストフィクスチャ](../../tests/conftest.py)
- [既存テストパターン](../../tests/)

## Deterministic Requirements
- pytest フィクスチャを活用
- AAAパターン（Arrange, Act, Assert）に従う
- テストデータベース（SQLite in-memory）を使用

## Structured Output
テストコード生成時に以下を含める:
- [ ] テスト関数名は `test_` で始める
- [ ] 認証が必要な場合は `test_user_token` フィクスチャを使用
- [ ] HTTPステータスコードとレスポンスボディを検証
- [ ] エラーケース（400, 401, 403, 404）のテスト
````



---

## :memo: 練習

以下の練習でカスタムインストラクションの理解を深めましょう：

### 練習1: カスタムインストラクションの作成と検証

1. フロントエンドリポジトリに移動
2. `.github/copilot-instructions.md` を作成（例題1を参考に）
3. 5つのセクション（概要、技術スタック、ガイドライン、構造、リソース）を記載
4. Copilot Chat で検証:
   ```text
   このプロジェクトの技術スタックは何ですか？
   ```
   → Copilot がインストラクションの内容を参照して回答するか確認

### 練習2: パス別インストラクションの作成と applyTo の動作確認

1. `.github/instructions/frontend.instructions.md` を作成
2. `applyTo: "**/*.{ts,tsx}"` を Front Matter に記載
3. TypeScript/React 固有のルールを記述
4. `.tsx` ファイルを開いて新しいコンポーネントを作成:
   ```text
   shadcn/uiのButtonとBadgeを使ったユーザーカードコンポーネントを作成して
   ```
   → Copilot がインストラクションのルールに従っているか確認

> 💡 **Tips**: カスタムインストラクションは「完璧」である必要はありません。最初は基本的な内容から始めて、プロジェクトの進行とともに進化させていくことが重要です。実際に使いながら改善していきましょう。

---

## まとめ

* **カスタムインストラクション** (`.github/copilot-instructions.md`) でプロジェクト固有のルールを Copilot に教える
* **パス別インストラクション** (`.github/instructions/*.instructions.md`) で特定のファイルパターンに特化したルールを定義
* **マルチリポジトリ構成** では各リポジトリに個別のカスタムインストラクションを配置
* **applyTo パターン** により、コンテキストを最適化してルールの適用範囲を制御
* **5つの推奨セクション** （What, What, How, Where, Resources）を GitHub 公式に従って構成
* **コンテキストローディング** （`#file:`, Markdown リンク）で関連ドキュメントを参照
* **段階的改善** - 不完全でも何もないよりはるかに効果的、実際に使いながら進化させる

次のセクション「③ カスタムチャットモードの設定」では、役割ベースの専門性を分離し、MCP ツールの境界を設定する方法を学びます。
