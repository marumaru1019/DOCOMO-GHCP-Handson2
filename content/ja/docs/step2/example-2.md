---
title: ②例を挙げる
categories: [GitHub Copilot, Prompt Engineering]
weight: 2
---

Copilot でコードを生成するときに、**「入力→出力」の例を提示**すると、単なる「ゼロショット」よりも正確な提案が得やすくなります。**1つだけ**例を与える「ワンショット(One-Shot)」と、**複数の例**を与える「少数ショット(Few-Shot)」の違いを、**Java**の簡単な文字列変換を例に見てみましょう。

---

## One-Shot（ワンショット）

### 概要
- **1つ**の「入力→出力」の例を提示  
- Copilot はその1つのパターンを参照し、新しいコードを類推

### :pen: プロンプト例

```text
下記の入出力例に基づいてJavaで関数を書いてください。

【例 (One-Shot)】
  入力: "Hello"
  出力: "HELLO"

要求:
- 関数名: toUpperCaseSingle
- 引数: String (1つの文字列)
- 戻り値: String (変換結果)
- Javaバージョン: 17
```

### :robot: 出力例

```java
public class StringConverter {
    public static String toUpperCaseSingle(String input) {
        return input.toUpperCase();
    }
}
```

Copilot は**1つの例 ("Hello"→"HELLO")**だけから「大文字変換ロジック」を推測し、最小限のコードを提案する可能性が高いです。

---

## Few-Shot（少数ショット）

### 概要
- **複数**の「入力→出力」例を与え、Copilot が共通パターンを抽出  
- **より複雑なロジック**でも精度アップが期待できる

### :pen: プロンプト例

```text
Copilot, 下記の入出力例を複数示します。
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

**複数の例**（"Hello"→"HELLO"、"test123"→"TEST123"）をもとに、**配列にも対応**したメソッドを提案してくれることが期待できます。

---

## :memo: 練習

1. **Zero-Shot で書かせてみる**  
   - 同じ大文字変換を「例を一切与えず」ただ「文字列を大文字に変換する関数をJavaで作って」と頼む  
   - どのような違いが生じるか観察  
2. **One-Shot**  
   - 今度は「入力:'abc'→出力:'ABC'」という**1つの例**をあげて、先ほどと提案がどう違うかを見る  
3. **Few-Shot**  
   - 2つ以上のサンプル入出力を挙げ、「同じ変換ロジックで複数入力に対応する関数を作って」と頼む  
   - 例を追加すると Copilot がよりパターンを深く理解して、複雑なコードを提案するかを確認  

---

## まとめ

- **One-Shot**: 1つのサンプル → シンプルな例なら十分  
- **Few-Shot**: 複数のサンプル → Copilot が共通ルールを抽出し、複雑なコードでも正確に提案  
- **練習**: Zero-Shot / One-Shot / Few-Shot の結果を比較して、**どれが一番狙い通りか**を確認してみよう

こうした**「例をあげる」**方法は「無例のゼロショット」よりも強力で、Copilot に**具体的なパターン**を学習させやすいテクニックです。