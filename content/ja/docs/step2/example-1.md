---
title: ①明確に指示を出す
categories: [GitHub Copilot, Prompt Engineering]
weight: 1
---

GitHub Copilot に「このコードを書いて」と漠然と伝えるだけでは、抽象的な提案が返ってくることが多いです。**チャットビュー**で「こういう仕様で書いて」と詳細を伝えると、**正しい文脈**がCopilotに伝わり、より期待通りのコードを生成してくれます。以下では、**チャットビュー**で使うプロンプトの書き方を例示します。

---

## 明確な指示を出す

### 1. 入力と出力を明記

#### :pen: プロンプト例
```text
次の仕様でメソッドを書いてほしいです:
- 言語: Java 17
- 関数名: convertArrayToJson
- 引数: String[] (文字列配列)
- 戻り値: JSON形式の文字列
  例: {"index0":"文字列0","index1":"文字列1",...}
```

#### :robot: 出力例

```java
public class ArrayConverter {
    public static String convertArrayToJson(String[] values) {
        StringBuilder sb = new StringBuilder();
        sb.append("{");
        for (int i = 0; i < values.length; i++) {
            sb.append("\"index").append(i).append("\": \"").append(values[i]).append("\"");
            if (i < values.length - 1) {
                sb.append(", ");
            }
        }
        sb.append("}");
        return sb.toString();
    }
}
```
Copilot が「引数が文字列配列」「JSON形式の戻り値」という仕様を把握しやすくなり、**明確なコード**を生成してくれます。

---

### 2. 関数名・変数名を指定

#### :pen: プロンプト例
```text
関数の名前を generateUserProfile にして書いてください。
言語: Java 17
要件:
- 引数: userName(String)
- 戻り値: JSON文字列
- 例: {"user":"Alice","role":"member"}
```

#### :robot: 出力例
```java
public class UserProfileGenerator {

    public static String generateUserProfile(String userName) {
        return String.format("{\"user\":\"%s\",\"role\":\"member\"}", userName);
    }
}
```
Copilot が**命名・戻り値形式**を正確に踏まえ、期待どおりのメソッドを作ってくれます。

---

### 3. 言語やフレームワーク、バージョンを明記

#### :pen: プロンプト例
```text
Copilot, Spring Boot 3.x を使っています。
Controllerクラスを作成し、「/hello」で "Hello, Spring" を返すコードを書いてください。
```

#### :robot: 出力例
```java
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, Spring";
    }
}
```
言語やフレームワーク（Spring Boot 3.x）を**明記する**ことで、**`@RestController` と `@GetMapping`** などを使った最適なスニペットを提案してくれます。

---

## まとめ

1. **チャットビューでのプロンプト**  
   - 「この言語で」「この名前で」「戻り値は○○」「フレームワークは△△」など、**具体的な条件**をまとめて書く  
2. **細部が曖昧だと抽象的な提案に**  
   - 入力 (引数) や戻り値の形式、バージョンを省くと、Copilot が推測しきれずに汎用的すぎるコードになる  
3. **最終的にはビルド＆テスト**  
   - 生成されたコードは**コンパイル・実行テスト**して動作を確認する

こうしたプロンプトを書くことで、Copilot による**正確かつ有用なコード生成**が期待できます。次のセクションでは、**例を挙げたり、段階的に指示**を与えるテクニックを学んでいきましょう。