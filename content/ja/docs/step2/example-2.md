---
title: ② 例を挙げる
categories: [GitHub Copilot, Prompt Engineering]
weight: 2
---


**GitHub Copilot** でコードを生成する際、**入力や出力の例**を提示すると、より正確な提案が得られます。**1つだけ**例を示す**One-Shot**（ワンショット）と、**複数**の例を示す**Few-Shot**（少数ショット）の違いを、**Java**での簡単な文字列変換を例に紹介します。

---

### :pen: 例題 – One-Shot

「1つの『入力→出力』例」を与える手法。  
Copilot は、その**単一例**からロジックを推測します。

#### プロンプト例

```text
次の仕様でメソッドを書いてほしいです:
- 言語: Java 17
- 関数名: toUpperCaseSingle
- 引数: String (1つの文字列)
- 戻り値: String (変換結果)

- 例:
  入力: "Hello" → 出力: "HELLO"
```

### :robot: 出力例

```java
public class StringConverter {
    public static String toUpperCaseSingle(String input) {
        return input.toUpperCase();
    }
}
```

Copilot は**1つの入力例**から大文字変換を推測し、メソッドを最小限に実装してくれます。

---

### :pen: 例題 – Few-Shot

「**複数の『入力→出力』例**」を与える手法。  
Copilot は**複数例**から共通パターンを学び、より複雑なロジックを推測できます。

#### プロンプト例

```text
下記の入出力例を複数示します。
同様の変換をするJava関数を書いてください。

【例1】
  入力: "Hello" 
  出力: "HELLO"

【例2】
  入力: "test123"
  出力: "TEST123"

要求:
- 関数名: toUpperCaseArray
- 引数: String[] (複数文字列)
- 戻り値: String[] (変換後の配列)
- Javaバージョン: 17
```

### :robot: 出力例

```java
public class StringArrayConverter {

    public static String[] toUpperCaseArray(String[] inputs) {
        String[] results = new String[inputs.length];
        for (int i = 0; i < inputs.length; i++) {
            results[i] = inputs[i].toUpperCase();
        }
        return results;
    }
}
```

Copilot は**複数の入出力例**（"Hello"→"HELLO", "test123"→"TEST123"）から、「複数文字列をまとめて変換するロジック」を抽出してくれます。

---

### :memo: 練習

1. **Zero-Shot**  
   - 一切例を示さず「大文字に変換する関数を作って」と頼んだ場合、Copilot がどんな提案をするか比べる  
2. **One-Shot → Few-Shot**  
   - 1つの例のみ vs. 2つ以上の例を示し、提案がどれほど変わるか観察  
3. **複雑な変換**  
   - "Hello"→"HELLO" に加え、「空白除去」「数字を保持」「特定文字を別シンボルに置き換え」など例を増やしてみる

---

## まとめ

- **One-Shot**: 1つの例を示す → **シンプル**なタスクなら十分  
- **Few-Shot**: 複数の例を示す → Copilot が**共通パターン**を学び、**複雑なロジック**でもより正確な提案  
- **実践**: Zero-Shot / One-Shot / Few-Shot を試し、**最も狙いに合った**結果を得られるよう確認  

こうして**例を示す**テクニックは「ゼロショット」よりも**的確**にCopilotへ意図を伝えられるため、**プロンプトエンジニアリング**として有力な方法です。