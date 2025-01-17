---
title: ⑦実装方針の策定 ～ 要件＋既存コードに基づいた詳細な実装指針
categories: [GitHub Copilot, Engineer Usecases]
weight: 7
---

新機能を追加するときは、**要件(何を作るか)** と **既存コード(変更対象)** を一緒に提示し、**実装方針**を Copilot に策定してもらうとスムーズです。ここでは、**「ポイントシステムを加える」**という要件と、**既存の `UserService.java` / `OrderService.java`** を例に示し、**どのような作業が必要かレベル**まで落とし込んだ**プロンプト**と、**Copilotの出力例**を提示します。

---

## 前提

### 要件ファイル (requirements.md)

```markdown
# Points System Requirements

1. 購入金額に応じてポイント加算
   - 1円 => 1ポイント
2. 合計ポイントが 100 を超えたら VIP 扱い (UserService 側でフラグ管理)
3. OrderService から purchase(...) 後に自動でポイントを追加
4. 後日、API や画面で "Your points" "VIP: true/false" を表示予定
5. DB 更新 or メモリ管理: まずはメモリ管理でもOK
```

### 既存の `UserService.java`

```java
package com.example;

public class UserService {

    // Currently just storing userName, no points or VIP.
    private String userName;

    public UserService(String userName) {
        this.userName = userName;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String newName) {
        this.userName = newName;
    }
}
```

### 既存の `OrderService.java`

```java
package com.example;

public class OrderService {

    // Currently just prints the order, no link to UserService.
    public void purchase(String itemName, int amount) {
        System.out.println("Order purchased: " + itemName + ", cost: " + amount);
    }
}
```

---

## プロンプト例

以下は、**Copilot** に「どのような作業が必要か、レベル**まで**具体的に落とし込んだ**実装方針**を出してほしい」と依頼するときのサンプルです。
この際に、UserService.javaとOrderService.javaをコンテキストにアタッチしてください。

```text
#file:requirements.md を踏まえ、実装方針を策定してください。
具体的に「どのクラスにどんなフィールド／メソッドを追加するか」「メインの作業工程は何か」など、詳細な作業リストを教えてください。
```

---

## Copilot 出力例（イメージ）

以下は、Copilot が返す可能性のある**詳細な実装方針**例。  
「**どういうクラスに何を追加し、どんな処理が必要か**」が明確になることで、**エンジニアが実際に作業しやすく**なります。

```
提案：ポイントシステム追加の詳細な実装方針

1) UserService の拡張
   - フィールド:
     * private int points = 0;  // 合計ポイント
     * private boolean vip = false;  // VIPフラグ
   - メソッド:
     * addPoints(int amount):
       - points += amount;
       - if (points > 100) { vip = true; }
     * getPoints(): int
     * isVip(): boolean
   - 作業工程:
     - フィールドとゲッター/セッター/ロジックを追加
     - 既存のコンストラクタは userName だけ -> 変更不要だが、points と vip の初期値を設定

2) OrderService と UserService を連携
   - OrderService内で purchase(...) 後に userService.addPoints(amount) を呼ぶ
   - userService インスタンスを注入 or渡す:
     - 例えば OrderService のコンストラクタで受け取る (DI対応は後でも可)
   - 作業工程:
     - OrderServiceに UserService フィールドを追加
     - purchase(itemName, amount) の最後で userService.addPoints(amount);

3) メインの作業工程まとめ
   - A: UserServiceクラス修正
     * int points, boolean vip フィールド
     * addPoints(int), getPoints(), isVip()
   - B: OrderServiceクラス修正
     * UserServiceをフィールドに持つ
     * purchaseの最後で userService.addPoints(amount)
   - C: 動作確認
     * メイン or テストコードで userService, orderService を生成
     * purchase("PC", 120000) → userService.getPoints() >=120000 → vip=true
     * consoleに "VIP: true" など確認

4) 今後の拡張
   - DB管理へ移行する際は points と vip を DBカラムに追加
   - Web層で "Your points" "VIP status" を表示するAPIを別途作成
   - Performance: if 追加ロジックが大きい場合、DIなどで設計見直し
```

Copilot はこのように**ステップごとの作業**や**フィールド追加の詳細**まで出してくる可能性があります。  
エンジニアはこれを叩き台にして**最終設計**を整えればOKです。

---

## まとめ

- **要件 (requirements.md)** と **既存コード (UserService.java / OrderService.java)** を **#file** で渡し → Copilot に**実装方針**を尋ねる  
- **具体的に「どんなフィールド/メソッドを足す？」「作業工程は？」** と聞く → Copilot が作業リストを提示  
- 提案はあくまで**参考** → エンジニアが**最終判断**や**設計調整**  
- こうして**要件 + 既存コード**をまとめて提供するだけで、**Copilot** が**段階的かつ詳細**なアプローチを示してくれるため、**新機能の実装**を効率よく始められます。