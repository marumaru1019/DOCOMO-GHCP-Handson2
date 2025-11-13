---
title: ② ツール
categories: [GitHub Copilot, 基礎]
weight: 2
---

## 1. ツールとは？

GitHub Copilot のチャットでは、**`#`ツール名**を使って便利な機能を呼び出すことができます。
これらは「ツール」と呼ばれ、コードに関する情報を簡単に取得したり、様々な作業を自動化したりできます。

**身近な例で説明すると：**
- `#selection` → 今選択しているコードについて質問
- `#terminalSelection` → ターミナルのエラーメッセージについて質問
- `#fetch` → ウェブサイトの内容を取得して分析

> **ポイント**
>
> * **チャット変数の参照** … `#-mention構文`で関連コンテキストを効率的に参照
> * **Agent mode での自動実行** … 自律的なコーディングワークフローで必要に応じてツールが実行される
> * **拡張機能とMCPサーバー** … 追加のツールを提供し、チャットプロンプトで利用可能

---

## 2. 利用可能なツール一覧

### 2.1 基本ツール

| ツール                          | 目的・説明                            |
| ---------------------------- | -------------------------------- |
| `#changes`                   | Git 変更一覧を取得し、差分を参照に会話            |
| `#codebase`                  | ワークスペース全体をコード検索して関連箇所を抽出         |
| `#editFiles`                 | ファイルの新規作成・編集を行うツールセット            |
| `#extensions`                | VS Code 拡張機能を検索・質問               |
| `#fetch`                     | 指定 URL の HTML/JSON などを取得して内容を利用  |
| `#findTestFiles`             | テストファイルを検索して一覧化                  |
| `#githubRepo`                | GitHub 上の公開リポジトリをコード検索           |
| `#new`                       | 新しい VS Code ワークスペースをスキャフォールド     |
| `#openSimpleBrowser`         | VS Code 内ブラウザでローカル Web アプリをプレビュー |
| `#problems`                  | Problems パネルのエラー・警告を取得           |
| `#readCellOutput`            | Notebook のセル出力を読み取り              |
| `#runCommands`               | ターミナルコマンドを実行し出力を取得               |
| `#runNotebooks`              | Notebook のセルを実行し出力を取得            |
| `#runTasks`                  | `tasks.json` に定義したタスクを実行         |
| `#runTests`                  | テストスイートを実行し結果を取得                 |
| `#search` / `#searchResults` | ワークスペース内のファイル検索＆結果取得             |
| `#selection`                 | エディタで選択中のテキストをコンテキスト追加           |
| `#terminalSelection`         | ターミナルで選択中のテキスト（コマンドなど）を追加        |
| `#terminalLastCommand`       | 直近で実行したターミナルコマンドを追加              |
| `#testFailure`               | テスト失敗情報をコンテキストに追加                |
| `#usages`                    | シンボル参照・実装・定義情報をまとめて取得            |
| `#VSCodeAPI`                 | VS Code 拡張 API のリファレンスを検索        |

### 2.2 ツールの使い分け

**コンテキスト参照系:**
- `#file` - 特定ファイルの内容を参照
- `#selection` - 現在選択している範囲
- `#codebase` - プロジェクト全体から自動検索

**実行・確認系:**
- `#runTests` - テスト実行と結果取得
- `#runCommands` - ターミナルコマンド実行
- `#problems` - エラーと警告の確認

**外部連携系:**
- `#fetch` - Web ページの内容取得
- `#githubRepo` - GitHub リポジトリ検索
- `#extensions` - VS Code 拡張機能の情報

---

## 3. 具体的なツールの使い方

### :pen: 例題1 - ターミナルエラーの解決

**状況:** ターミナルでコマンドを実行したらエラーメッセージが表示された

**やり方:**
1. ターミナルのエラーメッセージをマウスで選択
2. チャットに以下のように入力

```text
#terminalSelection このエラーを解決する方法を教えて
```

### :robot: 出力例

````
このエラーは npm パッケージのバージョン競合によるものです。

【エラーの原因】
- react@18.0.0 と react-dom@17.0.2 のバージョンが一致していません
- peer dependency の要件を満たしていません

【解決方法】

1. package.json を確認して react と react-dom のバージョンを統一:
   ```json
   "dependencies": {
     "react": "^18.0.0",
     "react-dom": "^18.0.0"
   }
   ```

2. node_modules を削除して再インストール:
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

3. それでも解決しない場合は、npm cache をクリア:
   ```bash
   npm cache clean --force
   npm install
   ```
````

**操作:**
1. 提案された解決方法を確認
2. 適切な方法を選択して実行
3. 必要に応じて追加質問

---

### :pen: 例題2 - Web ページからの情報取得

**状況:** Reactの最新情報をチェックしたい

**やり方:**
```text
#fetch https://react.dev Reactの最新バージョンについて教えて
```

### :robot: 出力例

```
React 公式サイトから最新情報を取得しました。

【最新バージョン】
React 18.3.0（2024年4月リリース）

【主な新機能】
1. Server Components（安定版）
   - サーバーサイドでのコンポーネントレンダリング
   - バンドルサイズの削減

2. Concurrent Features
   - useTransition による優先度付き更新
   - useDeferredValue による遅延レンダリング

3. Automatic Batching
   - 複数のstate更新を自動的にバッチ処理
   - パフォーマンスの向上

【移行ガイド】
React 17からの移行は比較的スムーズです。
破壊的変更は最小限に抑えられています。
```

**操作:**
1. 情報を確認
2. 必要に応じて詳細を追加質問
3. プロジェクトへの適用を検討

---

### :pen: 例題3 - GitHub リポジトリの調査

**状況:** Next.jsで使えるスタイリングライブラリの選択肢を知りたい

**やり方:**
```text
#githubRepo nextjsで使用できるスタイリング用のライブラリで使えそうなものをいくつか教えて
```

### :robot: 出力例

```
Next.js エコシステムで人気のスタイリングライブラリを調査しました。

【1. Tailwind CSS】
- GitHub Stars: 75k+
- 特徴: ユーティリティファーストのCSS フレームワーク
- Next.js統合: 公式サポート、簡単なセットアップ
- 利点: 高速な開発、小さなバンドルサイズ

【2. styled-components】
- GitHub Stars: 40k+
- 特徴: CSS-in-JS ライブラリ
- Next.js統合: Server Components対応版あり
- 利点: 動的なスタイリング、TypeScript サポート

【3. Emotion】
- GitHub Stars: 17k+
- 特徴: 高性能なCSS-in-JS
- Next.js統合: 良好なサポート
- 利点: 軽量、柔軟なAPI

【4. CSS Modules】
- 特徴: Next.js 組み込みサポート
- 利点: ゼロランタイム、スコープ付きCSS
- 追加インストール不要

【推奨】
- 小規模プロジェクト: Tailwind CSS
- 大規模プロジェクト: CSS Modules + Tailwind CSS
- 動的スタイリング重視: styled-components
```

**操作:**
1. 各ライブラリの特徴を比較
2. プロジェクトの要件に合わせて選択
3. セットアップ方法を追加で質問

---

## 4. 分からない時は直接聞こう

**使い方が分からないツールがある時は、直接聞いてみましょう**

```text
#terminalSelection このツールの使い方を教えて
```

どのツールでも「このツールの使い方を教えて」と聞けば、詳しい説明をしてくれます。

> 💡 **Tips**: ツールの組み合わせも効果的です。例えば `#file:app.tsx #codebase` のように複数のツールを組み合わせると、より精度の高い回答が得られます。

---

## 5. Agent mode でのツール制御

Agent mode では、使用するツールを制御できます。

### 5.1 Tool Pickerの使い方

![ツールの選択方法](../images/tool_select.png)

**手順:**
1. Agent mode のチャット入力欄右端の 🔧 アイコンをクリック
2. 使いたいツールだけにチェックを入れる
3. 不要なツールのチェックを外す

これにより、Agent mode が自動で作業する際に使用するツールを制限できます。

### 5.2 ツール制限の活用例

**セキュリティ重視の場合:**
- `#fetch` や `#githubRepo` を無効化
- ローカルファイルのみの操作に限定

**効率重視の場合:**
- 必要最小限のツールのみを有効化
- 不要な検索や実行を防止

**学習目的の場合:**
- 特定のツールのみを有効化
- ツールの動作を理解しやすくする

---

## 6. MCPサーバーでツールを拡張する

### 6.1 MCP とは

MCP（Model Context Protocol）は、**AIと外部システム（API/DB/ファイル/クラウド等）を安全に接続するための標準プロトコル**です。VS Code の Copilot Chat に MCP サーバーを追加すると、そのサーバーが提供する"ツール"が **Tool Picker** に現れ、Agent mode から呼び出せます。

### 6.2 導入メリット

* **手作業のコマンド/API呼び出しを会話化**（例：GitHubやAzure操作）
* **再利用・持ち運びが効く**：MCPはクライアント非依存（VS Code/JetBrains/Visual Studio 等）で同じサーバーを使い回せる
* **構成の見える化**：`mcp.json` で「どのツールを使って良いか」を明文化し、リポジトリに同梱可能

### 6.3 MCPサーバーのインストール方法

MCPサーバーをインストールする方法は3つあります。用途や環境に応じて使い分けましょう。

#### 方法1: VS Code 拡張機能からインストール

VS Code Marketplace で提供されている MCP サーバー拡張機能を直接インストールする方法です。

**手順:**

1. **拡張機能を検索**
   - 拡張機能ビュー（⇧⌘X / Ctrl+Shift+X）を開く
   - 検索ボックスに MCP サーバー名を入力（例：「Azure MCP」「GitHub MCP」）
   ![MCP Marketplace](../images/mcp-extension.png)

2. **インストール**
   - 目的の拡張機能を選択
   - 「Install」ボタンをクリック

3. **Tool Picker で確認**
   - Copilot Chat → Agent モードを選択
   - 🔧 **Tools** アイコンをクリック
   - 新しくインストールした MCP サーバーのツールが表示されることを確認

**利点:**
- VS Code 標準の拡張機能管理で一元化
- 自動更新に対応
- 設定が簡単

**適している場合:**
- 公式が拡張機能として提供している MCP サーバーを使いたい
- VS Code の標準的な管理方法で統一したい
- 複雑な設定なしにすぐ使いたい

#### 方法2: MCP Marketplace からインストール

> **Note**: この機能は現在プレビュー版です。設定で有効化する必要があります。

GitHub MCP レジストリと連携した専用マーケットプレイスから、様々な MCP サーバーをブラウズしてインストールできます。

**手順:**

1. **MCP Marketplace を有効化**
   - 設定で `chat.mcp.gallery.enabled` を `true` に設定

2. **MCP サーバーを検索**
   - 拡張機能ビュー（⇧⌘X / Ctrl+Shift+X）を開く
   - 検索ボックスに `@mcp` と入力
   ![MCP Marketplace](../images/mcp-marketplace.png)

3. **インストール**
   - 必要な MCP サーバーを選択
   - 「Install」ボタンをクリック

**利点:**
- 公式の GitHub MCP レジストリと連携
- 豊富な MCP サーバーのカタログ
- GUI で簡単にブラウズ・インストール
- バージョン管理が自動

**適している場合:**
- 様々な MCP サーバーを探したい
- コミュニティ提供の MCP サーバーを試したい
- GUI でインストール・管理したい

#### 方法3: 手動で mcp.json を編集

`.vscode/mcp.json` ファイルを直接編集して、MCP サーバーを設定する方法です。

**手順:**

1. **mcp.json ファイルを作成**
   - プロジェクトルートに `.vscode` フォルダを作成（存在しない場合）
   - `.vscode/mcp.json` ファイルを作成

2. **設定を記述**
   ```json
   {
     "mcpServers": {
       "github": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-github"]
       },
       "filesystem": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/directory"]
       }
     }
   }
   ```

3. **VS Code を再起動**
   - 設定を反映させるために VS Code を再起動

4. **Tool Picker で確認**
   - Copilot Chat で 🔧 **Tools** アイコンから追加されたツールを確認

**利点:**
- 詳細な設定が可能（コマンド引数、環境変数等）
- プロジェクト固有の設定として管理
- バージョン管理に含めてチーム共有が可能
- カスタム MCP サーバーの利用が可能

**適している場合:**
- プロジェクト固有の MCP サーバーを設定したい
- チームで設定を共有したい
- カスタム開発した MCP サーバーを使いたい
- 詳細なパラメータ設定が必要

**利用可能な MCP サーバー:**

[公式サンプル集](https://github.com/modelcontextprotocol/servers/tree/main)に、様々な MCP サーバーの一覧と詳細な設定方法が記載されています。

**主な MCP サーバー例:**
- **GitHub MCP Server**: GitHub API 操作（Issue/PR管理等）
- **Azure MCP Server**: Azure リソース管理、Bicep スキーマ取得
- **Database MCP Server**: データベースクエリ実行
- **File System MCP Server**: ファイル操作
- **Web Search MCP Server**: Web 検索機能

> **💡 Tips**: 各 MCP サーバーの詳細な設定方法（必要な引数や環境変数など）は、公式サンプル集の各サーバーのドキュメントを確認してください。サーバーごとにインストール手順と設定例が記載されています。

---

### :pen: 例題 - Azure MCP ServerでBicepテンプレート作成

**目的:** Storage AccountのBicepテンプレートを最新のAPI仕様で作成したい

**手順:**

1. [拡張機能のリンク](vscode:extension/ms-azuretools.vscode-azure-mcp-server) を開いてインストール
   ![Azure MCP Server 拡張機能](../images/azuremcp.png)

2. **Copilot Chat → Agent** を選択し、🔧 **Tools** で "Azure MCP Server" のツールを有効化
   ![Azure MCP Server 有効化](../images/azuremcpactivate.png)

3. チャットに以下のプロンプトを入力：

```text
#bicepschema
最新のapiVersionでStorage AccountのBicepテンプレートを作成してください。

要件：
- SKU: Standard_LRS
- 既存VNet統合は不要
- 冪等性を保つ設計
- 説明コメントも含める

```

### :robot: 出力例

Azure MCP Serverが最新のAPI仕様を取得し、以下のようなBicepテンプレートを生成：

```bicep
// ============================================================================
// Storage Account Bicep Template
// ============================================================================
// このテンプレートは、Azure Storage Accountを作成します。
// 最新のAPI Version: 2025-01-01を使用しています。
//
// 主な機能:
// - Standard_LRS SKUによるローカル冗長ストレージ
// - HTTPS通信の強制
// - TLS 1.2以上の要求
// - 階層型名前空間(HNS)の有効化オプション
// - パブリックアクセスの制御
// - 冪等性の保証（既存リソースがあっても安全にデプロイ可能）
// ============================================================================

// ----------------------------------------------------------------------------
// パラメータ定義
// ----------------------------------------------------------------------------

@description('Storage Accountの名前（グローバルに一意である必要があります）')
@minLength(3)
@maxLength(24)
param storageAccountName string

@description('Storage Accountをデプロイするリージョン')
param location string = resourceGroup().location

@description('Storage AccountのSKU（Standard_LRS: ローカル冗長ストレージ）')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param skuName string = 'Standard_LRS'

@description('Storage Accountの種類')
@allowed([
  'Storage'
  'StorageV2'
  'BlobStorage'
  'FileStorage'
  'BlockBlobStorage'
])
param kind string = 'StorageV2'

@description('アクセス層（Hot: 頻繁なアクセス、Cool: 低頻度アクセス）')
@allowed([
  'Hot'
  'Cool'
])
param accessTier string = 'Hot'

@description('階層型名前空間の有効化（Data Lake Storage Gen2機能）')
param enableHierarchicalNamespace bool = false

@description('パブリックネットワークアクセスの許可設定')
@allowed([
  'Enabled'
  'Disabled'
])
param publicNetworkAccess string = 'Enabled'

@description('Blob のパブリックアクセスを許可するかどうか')
param allowBlobPublicAccess bool = false

@description('共有キーアクセスを許可するかどうか（false推奨：Managed Identity使用）')
param allowSharedKeyAccess bool = true

@description('最小TLSバージョン')
@allowed([
  'TLS1_0'
  'TLS1_1'
  'TLS1_2'
  'TLS1_3'
])
param minimumTlsVersion string = 'TLS1_2'

@description('HTTPSトラフィックのみを許可するかどうか')
param supportsHttpsTrafficOnly bool = true

@description('リソースタグ')
param tags object = {
  Environment: 'Production'
  ManagedBy: 'Bicep'
  CreatedDate: '2025-10-01'
}

// ----------------------------------------------------------------------------
// 変数定義
// ----------------------------------------------------------------------------

// ネットワークルールのデフォルト設定
var networkAcls = {
  bypass: 'AzureServices'
  defaultAction: publicNetworkAccess == 'Disabled' ? 'Deny' : 'Allow'
  ipRules: []
  virtualNetworkRules: []
}

// ----------------------------------------------------------------------------
// リソース定義
// ----------------------------------------------------------------------------

@description('Azure Storage Account')
resource storageAccount 'Microsoft.Storage/storageAccounts@2025-01-01' = {
  name: storageAccountName
  location: location
  tags: tags

  // SKU設定（冗長性レベル）
  sku: {
    name: skuName
  }

  // Storage Accountの種類
  kind: kind

  // プロパティ設定
  properties: {
    // アクセス層の設定（BlobStorageまたはStorageV2の場合に有効）
    accessTier: accessTier

    // HTTPSトラフィックのみを許可
    supportsHttpsTrafficOnly: supportsHttpsTrafficOnly

    // 最小TLSバージョンの設定
    minimumTlsVersion: minimumTlsVersion

    // 階層型名前空間の有効化（Data Lake Storage Gen2）
    isHnsEnabled: enableHierarchicalNamespace

    // パブリックネットワークアクセスの制御
    publicNetworkAccess: publicNetworkAccess

    // Blobのパブリックアクセス制御
    allowBlobPublicAccess: allowBlobPublicAccess

    // 共有キーアクセスの制御（Managed Identity推奨）
    allowSharedKeyAccess: allowSharedKeyAccess

    // クロステナントレプリケーションの制御（セキュリティ強化）
    allowCrossTenantReplication: false

    // デフォルト認証をOAuthに設定（セキュリティ強化）
    defaultToOAuthAuthentication: true

    // ネットワークACLの設定
    networkAcls: networkAcls

    // 暗号化設定（デフォルトでMicrosoft管理キーを使用）
    encryption: {
      services: {
        blob: {
          enabled: true
          keyType: 'Account'
        }
        file: {
          enabled: true
          keyType: 'Account'
        }
        table: {
          enabled: true
          keyType: 'Account'
        }
        queue: {
          enabled: true
          keyType: 'Account'
        }
      }
      keySource: 'Microsoft.Storage'
      requireInfrastructureEncryption: false
    }
  }
}

// ----------------------------------------------------------------------------
// 出力
// ----------------------------------------------------------------------------

@description('作成されたStorage AccountのリソースID')
output storageAccountId string = storageAccount.id

@description('作成されたStorage Accountの名前')
output storageAccountName string = storageAccount.name

@description('Blob Serviceのプライマリエンドポイント')
output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob

@description('File Serviceのプライマリエンドポイント')
output fileEndpoint string = storageAccount.properties.primaryEndpoints.file

@description('Queue Serviceのプライマリエンドポイント')
output queueEndpoint string = storageAccount.properties.primaryEndpoints.queue

@description('Table Serviceのプライマリエンドポイント')
output tableEndpoint string = storageAccount.properties.primaryEndpoints.table

@description('Storage Accountのプライマリロケーション')
output primaryLocation string = storageAccount.properties.primaryLocation

```

**操作:**
1. 提案されたBicepテンプレートを確認
2. 必要に応じて「セキュリティをより強化して」等の追加指示
3. Agent modeが自動的に `#editFiles` ツールでファイル作成

### 💡 その他の活用例

**APIバージョン確認:**
```text
#bicepschema
Microsoft.KeyVault/vaults の最新 apiVersion を教えて
```

**スキーマ情報取得:**
```text
#bicepschema
Microsoft.KeyVault/vaults のスキーマ情報を教えて
```

> **補足:** Azure MCP Serverには Azure CLI 実行ツールも含まれており、既存リソースの状態確認や前提調査も会話で実行できます。

---

## :memo: 練習

以下の練習で実際にツールを使ってみましょう：

1. **基本ツールの練習**
   - `#selection` でコードを選択して質問
   - `#problems` でエラーや警告を確認
   - `#changes` で Git の変更内容を確認

2. **エラー解析の練習**
   - 何かコマンドを実行してエラーを出す
   - `#terminalSelection`で解決方法を聞いてみる

3. **情報収集の練習**
   - `#fetch`で好きなウェブサイトの情報を取得・要約してもらう
   - `#githubRepo`で興味のあるプロジェクトのコードを検索してもらう

4. **MCP拡張の練習**
   - MCP Marketplace から気になる MCP サーバーをインストール
   - Tool Pickerで新しいツールが表示されることを確認
   - 実際にツールを使ってタスクを実行

> 実際に使ってみることで、各ツールの便利さが実感できます。最初は基本的なツールから始めて、徐々に高度なツールやMCPサーバーに挑戦しましょう。

---

## まとめ

* **`#`記号** でツールを簡単に呼び出せる
* **シナリオ別** に使い分けることで効率的な開発が可能
* **分からない時は直接聞く** ことで新しいツールも覚えやすい
* **Agent mode** では自動でツールが選択されるが、Tool Pickerで制御も可能
* **MCP Marketplace** から簡単にツールを拡張可能（GUI操作）
* **MCPサーバー** で外部システム連携ツールを追加可能（Azure、GitHub等）
* **ツールの組み合わせ** により、より精度の高い回答が得られる

> MCPサーバーを活用することで、GitHub Copilotの基本ツールを大幅に拡張し、プロジェクト固有のワークフローに最適化された開発環境を構築できます。MCP Marketplaceの登場により、インストールと管理がさらに簡単になりました。
