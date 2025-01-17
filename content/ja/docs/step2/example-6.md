---
title: ⑥ 不要なコンテキストの排除
categories: [GitHub Copilot, Prompt Engineering]
weight: 6
---

Copilot は、**開いているファイル**や**チャット履歴**の文脈を参照します。しかし、その**文脈が膨大・不要**だと、**正しい情報**を読み取れずに誤ったコードが返される可能性があります。ここでは、**多数のAPI定義が書かれたMarkdownファイル**から、**少し特殊な(癖のある)APIのみ**抜き出して使いたい場面を例示します。さらに、**履歴を整理**する方法も紹介します。

---

## 例題: 膨大なAPI定義の中から「特殊なユーザーAPI」だけ参照

### シナリオ

- **既存ファイル**: `api_definitions.md` に複数のAPIが長々と書いてある  
- その中には**少し癖のある**「`GET /api/v1/users?region={region}&includeInactive=true`」APIがある  
- **新ファイル**: `UserApiClient.java`  
  - この特定APIを呼び出してユーザー一覧を取得したい  
  - 他のAPIは無視する  

#### Markdown ファイル (api_definitions.md) 例

```markdown
# API Definitions

## 1. GET /api/v1/products
- Description: Retrieve list of products.
- Query params: category (optional)
- Response: JSON array of product objects, each with id, name, price

## 2. POST /api/v1/orders
- Description: Create a new order.
- Request body: { "userId": number, "items": [...] }
- Response: { "orderId": 123, "status": "created" }

## 3. GET /api/v1/users?region={region}&includeInactive=true
- Description: Retrieve users in a specific region.
- Query params:
  - region: string (e.g. "EU", "US")
  - includeInactive: boolean (if true, returns inactive users as well)
- Response: JSON array of user objects
  e.g. [ { "id": 1, "name": "Alice", "region": "EU", "active": false }, ... ]

## 4. DELETE /api/v1/admin/cleanup
- Description: Performs admin-level cleanup.
- Query params: none
- Response: { "status": "ok" }

... (more definitions not relevant) ...
```

**実際にはさらに多くのAPIが記載**されているが、ここでは「`GET /api/v1/users?region={region}&includeInactive=true`」だけ使いたい、というケース。

---

## 不要なコンテキストを排除 (参照コードの抜粋)

### :pen: プロンプト例（チャットビュー）

```text
API 定義を参考にして新しいファイル "UserApiClient.java" をJava 17で作成し、下記を満たしてください:
- メソッド: fetchUsersByRegion(String region, boolean includeInactive)
- このAPIを呼び出してユーザー一覧を取得し、List<User> を返す
- (Userオブジェクトは {id, name, region, active} を持つ想定)
```

---

## :robot: 出力例 (イメージ)

```java
package com.example;

import java.net.*;
import java.io.*;
import java.util.*;
import com.fasterxml.jackson.databind.ObjectMapper;

public class UserApiClient {

    public List<User> fetchUsersByRegion(String region, boolean includeInactive) throws IOException {
        // Build URL with query params
        String urlStr = String.format("http://example.com/api/v1/users?region=%s&includeInactive=%b", 
                                       region, includeInactive);
        URL url = new URL(urlStr);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");

        // Read response
        try (InputStream in = conn.getInputStream()) {
            ObjectMapper mapper = new ObjectMapper();
            User[] usersArray = mapper.readValue(in, User[].class);
            return Arrays.asList(usersArray);
        }
    }
}
```

Copilot は、**MarkdownのAPI定義**「GET /api/v1/users?region={region}&includeInactive=true」を参照して、**HTTPリクエスト**や**JSONレスポンス**の処理を生成してくれます。

---

## 履歴を整理 – 新たなタスクでクリア

### 方法1: /clear コマンド
- 一部の環境では、チャット欄に `"/clear"` と入力すると**過去履歴が削除**される

### 方法2: 新規チャット開始
- **巻き戻しマーク**や **「+」アイコン**などで**新しいスレッド**を開始 → 以前の会話を参照しない  

### 方法3: 履歴を手動削除
- **巻き戻しマーク(ヒストリー)** から過去のメッセージを削除  
- これで不要な文脈をカット

---

## 練習

1. **別のAPI定義**を指定
   - POST /api/v1/orders だけ使いたい → Copilot がどう提案するか
2. **一度にまとめて頼む**  
   - 「全部のAPIを同時に実装して」とゼロショットで頼む → Copilot が混乱しないか比較
3. **履歴をあえて増やす → /clear**  
   - 前のタスクが大量にあった状態で**新しいタスク**を依頼 → 結果があいまいにならないかテストし、**/clear** で改善するか検証

---

## まとめ

- **膨大なAPI定義**から**1つ**だけ必要な場合、**不要部分はカット or 無視するよう指示**  
- **Copilot** は、**#file** 指定やファイル抜粋を通じて**必要な定義のみ**を読み込みやすくなる  
- **履歴が混み合う**と、**誤った文脈**が混ざりやすい → **/clear** コマンドや**新規チャット**でリセット  
- こうして**不要なコンテキストを排除**し、**履歴を整理**することで、Copilot に確実に「必要なコード」だけを意識させることができます。