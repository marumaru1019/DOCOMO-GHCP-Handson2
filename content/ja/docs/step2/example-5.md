---
title: ⑤関連コードを示す
categories: [GitHub Copilot, Prompt Engineering]
weight: 5
---

Copilot は、**すでにあるファイル**の内容を参照して、新しいファイルやメソッドを生成してくれます。ここでは **`NumberUtils.java`** を事前に用意し、そのメソッドを**新しいファイル**でも利用する例を示します。**関連コードをプロンプトに含める**ことがポイントです。

---

## 例題

### シナリオ

- 既存の **`NumberUtils.java`** には複数の数値操作メソッドがある（`triple`や`square`など）。
- **新しく** `ArrayMultiplier.java` を作って、`NumberUtils.triple()` を活用し、配列要素を3倍化したい。

---

## NumberUtils.java（既存の全体コード）

```java
public class NumberUtils {

    /**
     * Returns three times the input value.
     */
    public static int triple(int x) {
        return x * 3;
    }

    /**
     * Returns the square of the input value.
     */
    public static int square(int x) {
        return x * x;
    }

    /**
     * Returns the sum of two integers.
     */
    public static int sum(int a, int b) {
        return a + b;
    }
}
```

このファイルを**Copilot が参照**にして、新しいファイル `ArrayMultiplier.java` を作成します。

---

## プロンプト例

```text

#file:NumberUtils.java

要件に従って、新しいクラス `ArrayMultiplier` を作成してください。
要件:
- クラス名: ArrayMultiplier
- メソッド: multiplyArrayByTriple(int[] arr)
  - 戻り値: int[] (配列要素をNumberUtils.tripleで3倍にしたものを返す)
- Javaバージョン: 17
```

---

## :robot: 出力例（イメージ）

```java
public class ArrayMultiplier {

    public static int[] multiplyArrayByTriple(int[] arr) {
        int[] result = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            result[i] = NumberUtils.triple(arr[i]);
        }
        return result;
    }
}
```

Copilot は、**既存の `NumberUtils.triple`** メソッドを参照し、**新規クラス**でそれを呼び出すコードを提案。  

---

## 練習

1. **追加依頼**  
   - 「`NumberUtils.square()` も使ってみて」と頼んで `ArrayMultiplier` に `multiplyAndSquare(int[] arr)` を追加させる  
2. **不要ファイルを閉じる**  
   - 意図的に `UnrelatedFile.java` などを閉じてから、Copilot に依頼し、**無関係な提案**が減るか試す  
3. **プラットフォーム指定**  
   - 「Spring Boot環境で使うために、`ArrayMultiplier`に `@Service`アノテーションを付けて」 → Copilot が `import org.springframework.stereotype.Service;` 等を補ってくれるか観察

---

## まとめ

- **既存コード** (e.g. `NumberUtils.java`) を参照させるには、**開いている状態**か **#file指定**で明確に伝える  
- Copilot は既存メソッド (`triple`, `square`, `sum`) を認識し、**新しいクラス**でも自然に呼び出すロジックを生成  
- **不要なファイル**を閉じたり、**必要なファイルだけ選択**することで、Copilot が**誤った推測**をしにくくなる  

こうして**関連コードを示す**ことで、Copilot が**既存実装**を踏襲しながら**新しいファイル/メソッド**を的確に書いてくれます。