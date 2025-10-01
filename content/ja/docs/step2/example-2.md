---
title: ② ツール
categories: [GitHub Copilot, Agent Mode]
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

---

## 3. 具体的なツールの使い方

### :pen: 例題

よく使うツールの実際の使い方を見てみましょう。

### 💡 シナリオ1：ターミナルでエラーが出た時

**状況：** ターミナルでコマンドを実行したらエラーメッセージが表示された

**やり方：**
1. ターミナルのエラーメッセージをマウスで選択
2. チャットに以下のように入力

```text
#terminalSelection このエラーを解決する方法を教えて
```

**結果：** Copilotがエラーの原因と解決方法を詳しく説明してくれます

---

### 💡 シナリオ2：React公式サイトの情報が知りたい時

**状況：** Reactの最新情報をチェックしたい

**やり方：**
```text
#fetch https://react.dev Reactの最新バージョンについて教えて
```

**結果：** React公式サイトから最新バージョンの情報を取得し、要約してくれます

### 💡 シナリオ3：Next.jsのスタイリングライブラリを調べたい時

**状況：** Next.jsで使えるスタイリングライブラリの選択肢を知りたい

**やり方：**
```text
#githubRepo nextjsで使用できるスタイリング用のライブラリで使えそうなものをいくつか教えて
```

**結果：** Next.jsエコシステムから人気のスタイリングライブラリ（Tailwind CSS、Styled Components、Chakra UI等）とその特徴を教えてくれます

## 4. 分からない時は直接聞こう

**使い方が分からないツールがある時は、直接聞いてみましょう**

```text
#terminalSelection このツールの使い方を教えて
```

どのツールでも「このツールの使い方を教えて」と聞けば、詳しい説明をしてくれます。

---

## 5. Agent mode でのツール制御

Agent mode では、使用するツールを制御できます。


**Tool Pickerの使い方：**
![ツールの選択方法](../images/tool_select.png)
1. Agent mode のチャット入力欄右端の 🔧 アイコンをクリック
2. 使いたいツールだけにチェックを入れる

これにより、Agent mode が自動で作業する際に使用するツールを制限できます。

---

## 6. MCPサーバーでツールを拡張する

### 6.1 MCP とは

MCP（Model Context Protocol）は、**AIと外部システム（API/DB/ファイル/クラウド等）を安全に接続するための標準プロトコル**です。VS Code の Copilot Chat に MCP サーバーを追加すると、そのサーバーが提供する"ツール"が **Tool Picker** に現れ、Agent mode から呼び出せます。

### 6.2 導入メリット

* **手作業のコマンド/API呼び出しを会話化**（例：GitHubやAzure操作）
* **再利用・持ち運びが効く**：MCPはクライアント非依存（VS Code/JetBrains/Visual Studio 等）で同じサーバーを使い回せる
* **構成の見える化**：`mcp.json` で「どのツールを使って良いか」を明文化し、リポジトリに同梱可能

### 6.3 ソース
[公式サンプル集](https://github.com/modelcontextprotocol/servers/tree/main) に、どんな MCP サーバーが使えるのかの一覧が記載しているので御覧ください。

### :pen: 例題：Azure MCP ServerでBicepテンプレート作成

**目的：** Storage AccountのBicepテンプレートを最新のAPI仕様で作成したい

**手順：**

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

**操作：**
1. 提案されたBicepテンプレートを確認
2. 必要に応じて「セキュリティをより強化して」等の追加指示
3. Agent modeが自動的に `#editFiles` ツールでファイル作成

### 💡 その他の活用例

**APIバージョン確認：**
```text
#bicepschema
Microsoft.KeyVault/vaults の最新 apiVersion を教えて
```

**スキーマ情報取得：**
```text
#bicepschema
Microsoft.KeyVault/vaults のスキーマ情報を教えて
```

> **補足：** Azure MCP Serverには Azure CLI 実行ツールも含まれており、既存リソースの状態確認や前提調査も会話で実行できます。

---

## :memo: 練習

以下の練習で実際にツールを使ってみましょう：

1. **エラー解析の練習**
   何かコマンドを実行してエラーを出し、`#terminalSelection`で解決方法を聞いてみる

2. **情報収集の練習**
   `#fetch`で好きなウェブサイトの情報を取得・要約してもらう

3. **コード研究の練習**
   `#githubRepo`で興味のあるプロジェクトのコードを検索してもらう

4. **MCP拡張の練習（上級者向け）**
   - `.vscode/mcp.json` を作成してシンプルなMCPサーバーを追加してみる
   - Tool Pickerで新しいツールが表示されることを確認する

---

## 7. まとめ

* **`#`記号** でツールを簡単に呼び出せる
* **シナリオ別** に使い分けることで効率的な開発が可能
* **分からない時は直接聞く** ことで新しいツールも覚えやすい
* **Agent mode** では自動でツールが選択されるが、Tool Pickerで制御も可能
* **MCPサーバー** で外部システム連携ツールを追加可能（Azure、GitHub等）
* **実際に使ってみる** ことで、各ツールの便利さが実感できる

> MCPサーバーを活用することで、GitHub Copilotの基本ツールを大幅に拡張し、プロジェクト固有のワークフローに最適化された開発環境を構築できます。
