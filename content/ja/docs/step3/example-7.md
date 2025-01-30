---
title: ⑦ 実装方針の策定
categories: [GitHub Copilot, Engineer Usecases]
weight: 7
---

新機能を追加するとき、**要件 (何を作るか)** と **既存コード (どこに手を加えるか)** を同時に提示し、**実装方針**を Copilot に考えてもらうと効率的です。ここでは「**ポイントシステムを加える**」という要件と、**既存の `UserService.java` / `OrderService.java`** を例に示し、**Copilot にどのような作業が必要かレベルまで落とし込んだ指針を出してもらう**かを紹介します。

---

## 前提ファイル

### requirements.md (要件ファイル)

```markdown
1. 購入金額に応じてポイント加算
   - 1円 => 1ポイント
2. 合計ポイントが100を超えたらVIP扱い (UserService側でフラグ管理)
3. OrderServiceから purchase(...) 後に自動でポイントを追加
4. 後日、APIや画面で "Your points" "VIP: true/false" を表示予定
5. DB管理 or メモリ管理: まずはメモリ管理でもOK
```

### UserService.java (既存コード)

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

### OrderService.java (既存コード)

```java
package com.example;

public class OrderService {

    // Currently just prints the order, no link to UserService.
    public void purchase(String itemName, int amount) {
        System.out.println("Order purchased: " + itemName + ", cost: " + amount);
    }
}
```

このように、**ポイント管理**がまだ実装されていないコードと、**要件定義**がある状況を前提に、Copilot に「どう実装すればいいか？」の**詳細プラン**を聞き出します。

---

### :pen: 例題 – 実装方針を Copilot に聞く

たとえば、以下のように Copilot へ依頼します:

```text
#file:requirements.md を踏まえて、
#file:UserService.java と #file:OrderService.javaを修正してポイントシステムを追加したい。
どのクラスにどのフィールドやメソッドを加え、どんな作業手順になるかレベルで詳細な実装方針を出してください。
```

**ポイント**  
- 「要件ファイル (requirements.md)」と「既存コード (UserService, OrderService)」を **#file** や隣接タブで同時参照させる  
- 「何を加えるべき？」 「どの工程でやる？」と**具体的なタスク**を聞く

---

### :robot: 出力例

Copilot が返す**実装方針の例**:

```
提案：ポイントシステム追加の詳しい実装プラン

1) UserService クラス拡張
   - フィールド:
     private int points = 0; //合計ポイント
     private boolean vip = false; //100pt超えたらVIP
   - メソッド:
     public void addPoints(int amount):
       -> points += amount;
       -> if (points > 100) vip = true;
     public int getPoints():
     public boolean isVip():
   - 作業工程:
     A. フィールドとゲッター/セッターを追加
     B. コンストラクタで points=0 / vip=false に初期化

2) OrderService との連携
   - OrderService で purchase(...) のあとに userService.addPoints(amount)を呼ぶ
   - フィールド or 引数で userServiceを渡す (DIは後で)
   - 作業工程:
     A. OrderServiceに UserService userService を追加
     B. purchase(...)の末尾で userService.addPoints(amount);

3) 全体のステップ
   - 1) UserService修正
   - 2) OrderService修正
   - 3) テスト/動作確認
     -> mainや単体テストで userService + orderService生成
     -> purchase("PC", 120000) => userService.getPoints()=120000 => VIP=true
   - 4) 今後: DB移行, 画面表示, etc.
```

このように、**何を追加**し、**どう呼び出すか**や**作業順**などをCopilotが提示します。エンジニアはこれを**たたき台**にさらに細かい仕様を詰められます。

---

### :memo: 練習

1. **実際に実装してみる**  
   - Copilotの出したプランを基に、UserServiceやOrderServiceを**実際に修正**  
   - Copilot に「実装コードも書いて」と頼む  
2. **要件を足す**  
   - 例: 「ポイント有効期限がある」「VIP後もポイントが減ればVIP解除」 → Copilotがどのような追加案を提示するか  
3. **改修後のレビュー**  
   - できたら「この実装案で改善点ある？」とCopilotに聞き、**レビューコメント**をもらう

---

## まとめ

- **要件ファイル + 既存コード**をセットで提示 → Copilotが**具体的な実装方針**を提案  
- 「どのクラスに何のフィールド/メソッド」レベルで作業を落とし込む → **エンジニア**はそれを**叩き台**に設計調整  
- **実装後、テストやレビュー**を行い、仕上げていく流れがスムーズ  

こうして**Copilot**を「**要件**と**既存コード**を合わせた文脈」で利用すれば、**新機能追加の方針**が素早く固まり、**開発効率**がアップします。