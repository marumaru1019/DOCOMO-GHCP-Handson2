---
title: ⑩ Copilot Edits
categories: [GitHub Copilot, Engineer Usecases]
weight: 10
---

Copilot Edits という機能を使うと、直接エディター内でファイルの中身を生成・変更することができます。この機能を活用することで、複数のファイルを一括で作成・修正する際に効率的に作業できます。ここでは、複数のJavaファイルを新規作成する例を紹介します。

---

## Copilot Edits を使ったファイルの新規作成

**呼び出し方法**:
- 「Copilot Chat」アイコンの右隣にある下矢印を押し、「Copilot Edits を開く」を選択
- ショートカットで呼び出す(VS Code 限定):
  - **Windows**: `Ctrl+Shift+I`
  - **Mac**: `command+shift+I`

![Image](https://github.com/user-attachments/assets/18d20d01-a707-4058-bd93-3169f9b164b7)

### :pen: プロンプト例

```text
以下の要件に沿った新規Javaファイルを3つ作成してください。

【ファイル1: UserService.java】
- クラス名: UserService
- メソッド: getUserById(int id): User
- ユーザー情報を保持するUserクラスも定義する（フィールド: id, name）

【ファイル2: OrderService.java】
- クラス名: OrderService
- メソッド: createOrder(int userId, double amount): Order
- 注文情報を保持するOrderクラスも定義する（フィールド: orderId, userId, amount）

【ファイル3: ProductService.java】
- クラス名: ProductService
- メソッド: listProducts(): List<Product>
- 製品情報を保持するProductクラスも定義する（フィールド: id, productName, price）

```

このプロンプトでは、各ファイルに必要なクラスとメソッドの仕様を明確に指示しており、Copilot Editsは指定された内容に基づいて複数のファイルを新規作成します。

---

## :robot: 出力例

### UserService.java

```java
public class UserService {

    /**
     * 指定されたユーザーIDに基づいてUserオブジェクトを返します。
     */
    public User getUserById(int id) {
        // 簡単な例として、固定のユーザー名を返す
        return new User(id, "User" + id);
    }
}

// ユーザー情報を保持するクラス
class User {
    private int id;
    private String name;

    public User(int id, String name) {
        this.id = id;
        this.name = name;
    }

    // getter, setter省略
}
```

### OrderService.java

```java
public class OrderService {

    /**
     * ユーザーIDと注文金額に基づいて注文を作成します。
     */
    public Order createOrder(int userId, double amount) {
        // 例として、注文IDを固定値で生成
        return new Order(1001, userId, amount);
    }
}

// 注文情報を保持するクラス
class Order {
    private int orderId;
    private int userId;
    private double amount;

    public Order(int orderId, int userId, double amount) {
        this.orderId = orderId;
        this.userId = userId;
        this.amount = amount;
    }

    // getter, setter省略
}
```

### ProductService.java

```java
import java.util.ArrayList;
import java.util.List;

public class ProductService {

    /**
     * 利用可能な製品の一覧を返します。
     */
    public List<Product> listProducts() {
        List<Product> products = new ArrayList<>();
        products.add(new Product(1, "Product A", 9.99));
        products.add(new Product(2, "Product B", 19.99));
        return products;
    }
}

// 製品情報を保持するクラス
class Product {
    private int id;
    private String productName;
    private double price;

    public Product(int id, String productName, double price) {
        this.id = id;
        this.productName = productName;
        this.price = price;
    }

    // getter, setter省略
}
```

Copilot Editsは、上記のプロンプトに基づき、3つのファイルをそれぞれ生成し、指定されたクラスやメソッドを自動的に実装するコードを提案します。

コードを採択する場合は、採用をクリックすることでエディターにコードを挿入できます。

![Image](https://github.com/user-attachments/assets/993950f1-ccf2-4124-ab0b-d00b9794cfd6)

---

## :bulb: Tips

**エージェント機能の活用**:

Copilot Editsのエージェントモード（プレビュー機能）を有効にすると、複数ファイルにまたがる変更を自律的に処理できます。

**呼び出し方法**:
- エージェントモードを使用するには VS Code Insiders が必要になります。VS Code Insiders をインストールし、Copilot Edits を開いた状態で、チャット入力欄にあるモードを「Edits」→「Agent」に切り替えることで利用可能です。
![Image](https://github.com/user-attachments/assets/ec6916bd-5781-4aee-9d0a-3264f1eedfe1)

### :pen: プロンプト例
```text
UserService, OrderService, ProductService全体で共通のログ出力形式を追加してください
```

### :robot: 出力例

#### UserService.java
```java
import java.util.logging.Logger;

public class UserService {
    private static final Logger logger = Logger.getLogger(UserService.class.getName());

    public User getUserById(int id) {
        logger.info("Getting user by ID: " + id);
        // ...implementation...
        return new User(id, "Sample User");
    }

    public static class User {
        private int id;
        private String name;

        public User(int id, String name) {
            this.id = id;
            this.name = name;
        }

        public int getId() {
            return id;
        }

        public String getName() {
            return name;
        }
    }
}
```

#### OrderService.java
```java
import java.util.logging.Logger;

public class OrderService {
    private static final Logger logger = Logger.getLogger(OrderService.class.getName());

    public Order createOrder(int userId, double amount) {
        logger.info("Creating order for user ID: " + userId + ", amount: " + amount);
        // ...implementation...
        return new Order(1, userId, amount);
    }

    public static class Order {
        private int orderId;
        private int userId;
        private double amount;

        public Order(int orderId, int userId, double amount) {
            this.orderId = orderId;
            this.userId = userId;
            this.amount = amount;
        }

        public int getOrderId() {
            return orderId;
        }

        public int getUserId() {
            return userId;
        }

        public double getAmount() {
            return amount;
        }
    }
}
```

### ProductService.java
```java
import java.util.logging.Logger;
import java.util.ArrayList;
import java.util.List;

public class ProductService {
    private static final Logger logger = Logger.getLogger(ProductService.class.getName());

    public List<Product> listProducts() {
        logger.info("Listing products");
        List<Product> products = new ArrayList<>();
        products.add(new Product(1, "Sample Product", 99.99));
        return products;
    }

    public static class Product {
        private int id;
        private String productName;
        private double price;

        public Product(int id, String productName, double price) {
            this.id = id;
            this.productName = productName;
            this.price = price;
        }

        public int getId() {
            return id;
        }

        public String getProductName() {
            return productName;
        }

        public double getPrice() {
            return price;
        }
    }
}
```

変更を受け入れる場合は、「Keep」をクリックしてください。
![Image](https://github.com/user-attachments/assets/622ed526-4444-4188-b99d-2685ee5f1c3c)

### :pen: 追加で指示をする
エージェントモードではエディタの直接編集に加え、ターミナルでのコマンドの自動実行もサポートされています。
例えば、`生成したコードの動作確認を行ってください`のような指示を出すことで、動作確認をするためのターミナルのコマンドを自動生成して、実行することができます。その際にエラーが発生すると自律的にエラーの原因を調査して修正案を提案してくれます。


**Continue を押すことで、ターミナルに自動的にコマンドが入力され、実行されます。**
![Image](https://github.com/user-attachments/assets/e56176d6-3cdc-455c-b30c-615167d86c59)


**実行され問題なくコンパイルができたことを検知しています。**
![Image](https://github.com/user-attachments/assets/a115912d-5b62-4388-a153-9d3ec21c3635)

---

## :memo: 練習

1. **追加メソッドの依頼**
   - 例えば、「UserServiceにupdateUserメソッド、OrderServiceにcancelOrderメソッド、ProductServiceにgetProductByIdメソッドを追加してください」と段階的に依頼してみる。
2. **エージェントとの連携**
   - Copilot Editsエージェントモードを使い、複数ファイルで共通するエラーハンドリングやログ出力の統一を指示し、全体最適化の提案を受ける。
---

## 6. まとめ

- **Copilot Edits** を利用することで、既存ファイルがない状態からでも複数のJavaファイルを新規作成でき、各ファイルに必要なクラスやメソッドを自動生成できます。
- **エージェント機能**を併用すれば、複数ファイルにまたがる共通部分の修正や統一的な改善が容易になり、プロジェクト全体の整合性が向上します。
- 練習として、追加メソッドの依頼やエージェント機能の活用を試し、Copilot Editsの提案精度を確認しましょう。
```