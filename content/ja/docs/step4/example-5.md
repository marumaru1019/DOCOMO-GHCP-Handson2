---
title: ⑤トラブルシュート手順書の作成
categories: [運用, GitHub Copilot]
weight: 5
---

**トラブルシュート**や **運用**の現場では、**ライブラリ不足**などのコード起因だけでなく、**環境設定**や **アクセス権**といった外部要因のエラーが発生するケースも多々あります。ここでは、**環境が原因**でエラーが起きたときの例を提示し、**Copilot** を使って**トラブルシュート手順書**をまとめる流れを示します。

---

## シナリオ：Azure 上の環境変数が設定されておらず、アプリが起動に失敗

### 1. 障害の状況

- **Javaアプリ**が Azure Web App で動作しているはずだが、**起動時に DB接続エラー**を起こしている  
- ログには「**DB_HOST env variable not found**」など、環境変数が設定されていないようなメッセージが記録されている  
- ローカル環境では動くが、本番環境 (Azure) では落ちる

### 2. ログサンプル

```plaintext
2023-08-15 09:12:34.567 ERROR [MyApp]
Caused by: java.sql.SQLException: Unknown host 'null'
    at com.example.DBConnection.open(DBConnection.java:45)
    ... 
[Environment Check] DB_HOST not set or empty
```

#### エラーのポイント

- `DB_HOST` という環境変数が **null** になっている → そのため DBホスト名が分からず接続に失敗


### 3. Copilot を使ってトラブルシュート手順書を自動生成

#### (1) プロンプト例

```text
Copilot, 下記ログと状況を参考に、環境変数が未設定でアプリが起動失敗する問題に関するトラブルシュート手順書をMarkdownで作ってください。

ログ抜粋:
"DB_HOST not set or empty" 
"Unknown host 'null'"

状況:
- Azure Web App上で動かすJavaアプリ
- DB_HOSTが設定されていない

手順書には以下を含めて:
1) 原因の概要
2) 対処手順 (Azure PortalやCLIで環境変数を設定する方法)
3) 再発防止策
```

#### (2) Copilot 出力例（イメージ）

```markdown
# トラブルシュート手順書 - 環境変数(DB_HOST)未設定による起動失敗

## 1. 原因の概要
- アプリ起動時に `DB_HOST` 環境変数が未設定のまま
- ログ内に "DB_HOST not set or empty" とあり、DB接続先が `null` となって失敗

## 2. 対処手順

### 2-1. Azure Portal での環境変数設定
1. Azure Portal にログイン → App Services で対象の Web App を選択
2. 「Configuration」→「Application Settings」を開く
3. 「New application setting」をクリックし、Name = `DB_HOST`, Value = `your-db-hostname` を入力
4. 保存して、Web App を再起動

### 2-2. Azure CLI で設定する場合
az login
az webapp config appsettings set \
  --resource-group MyResourceGroup \
  --name MyAppService \
  --settings DB_HOST=your-db-hostname

- これで `DB_HOST` が設定される
- 最後に `az webapp restart --resource-group MyResourceGroup --name MyAppService` で再起動

### 2-3. Javaコード側の確認
- `System.getenv("DB_HOST")` でちゃんと値が取得できるか確認
- 取得できなければログに警告を表示 → 事前に気づきやすくする

## 3. 再発防止策
1. **環境変数チェック**:
   - アプリ起動時に必須の環境変数がない場合、エラーメッセージをはっきり出す
2. **CI/CDパイプライン**:
   - デプロイ前に環境変数が設定されているか自動テストorスクリプトで検証
3. **ドキュメント化**:
   - 今回の設定内容を運用手順書に明記し、セットアップ時に参照できるようにする
```

> これにより **「原因の概要」「具体的な対処」「Azure CLI コマンド例」「再発防止策」** を **Markdown** で得られ、手順書をチームに共有できる。

---

---

## まとめ

1. **環境起因のエラー**（環境変数未設定・Firewall設定ミスなど）も、**ログ**と**該当コード**を**Copilot**に渡す → **原因・対処手順**を自動でMarkdown化  
2. **Azure CLI**など**具体的コマンド**を含めさせれば、**手順書**として非常に再現性が高い  
3. **最終確認**は**人間**が行い、**手順書**を共有 → チーム全員が**同じ流れ**で再現できる  

こうして**環境設定ミス**のような**運用フェーズ**のトラブルでも、**Copilot**を使って**手順書作成**をスピーディに行い、**後続の対応**や**再発防止**がスムーズになります。