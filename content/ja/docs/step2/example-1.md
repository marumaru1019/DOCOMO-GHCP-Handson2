---
title: ① 明確に指示を出す
categories: [GitHub Copilot, Prompt Engineering]
weight: 1
---

**GitHub Copilot** では、「**このコードを書いて**」と曖昧に伝えるよりも、**言語**や**関数名**、**入出力の仕様**などを明確に指定した方が、狙いどおりのコードを生成しやすくなります。ここでは、チャットビューで書くプロンプトの具体例を紹介します。

---

### :pen: 例題 – 入力と出力を明記

#### プロンプト例

```text
次の仕様でメソッドを書いてほしいです:
- 言語: Java 17
- 関数名: convertArrayToJson
- 引数: String[] (文字列配列)
- 戻り値: JSON形式の文字列
  例: {"index0":"文字列0","index1":"文字列1",...}
```

### :robot: 出力例

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

Copilot は**言語 (Java 17)**、**関数名 (convertArrayToJson)**、**引数・戻り値**を知ることで、意図したコードを生成しやすくなります。

---

### :pen: 例題 – 関数名や変数名を指定

#### プロンプト例

```text
関数の名前を generateUserProfile にして書いてください。
言語: Java 17
要件:
- 引数: userName(String)
- 戻り値: JSON文字列
- 例: {"user":"Alice","role":"member"}
```

### :robot: 出力例

```java
public class UserProfileGenerator {

    public static String generateUserProfile(String userName) {
        return String.format("{\"user\":\"%s\",\"role\":\"member\"}", userName);
    }
}
```

指定どおりの関数名とJSON形式の戻り値を出力してくれます。

---

### :pen: 例題 – フレームワークやバージョンを明記

#### プロンプト例

```text
Spring Boot 3.x を使っています。
Controllerクラスを作成し、「/hello」で "Hello, Spring" を返すコードを書いてください。
```

### :robot: 出力例

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, Spring";
    }
}
```

**Spring Boot 3.x** と **エンドポイント**を指定すると、**`@RestController`** や **`@GetMapping`** などが自動的に推測されます。

---

### :bulb: Tips
Copilot ポリシーで、「Suggestions matching public code」が Block にしている場合、コードの提案とその周囲の約 150 文字のコードに関するコードを公開コードと照合し、一致した場合にコードの生成がブロックされます。
ポリシーを回避するためにパブリックコードと一致させないために、プロンプトにて関数名やクラス名を独自のものに指定することで、一致を避けることができます。

#### プロンプト例
```text
〇〇に関する処理を行うコードを書いてください。
クラス名には、〇〇というプレフィックスをつけ、関数名には、〇〇というプレフィックスをつけてください。
```

---

### :memo: 練習

1. **さらに詳細を指定**
   - 「テストフレームワークに JUnit5 を使う」「戻り値はList型」など、追加要件を盛り込んでみましょう
2. **抽象的 vs 具体的**
   - わざと曖昧に「ユーザー情報を返すメソッドを書いて」と頼む場合と、詳細に「引数/戻り値/バージョン」まで指定する場合を比べて、出力がどう変わるか観察
3. **ビルド＆実行**
   - 生成コードをコンパイル・テストして動作確認 → 修正点があれば Copilot に再指示してアップデート

---

## まとめ

- **言語・関数名・入出力**を明確に指定すると、Copilot はより正確なコードを提案
- フレームワークやバージョン (例: Spring Boot 3.x, Java 17) も書いておけば、**最適なアノテーション**やライブラリ呼び出しを推測
- **抽象的**な指示だとCopilotも**汎用的**なコードを返す → 目的に沿わない可能性がある
- 生成後は**コンパイル＆テスト**して正しく動くか確認し、必要に応じて修正する

このように、**Copilot** を**上手にガイド**することで、コード生成の生産性を最大限に引き出せます。