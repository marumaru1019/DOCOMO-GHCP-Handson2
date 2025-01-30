---
title: ⑤ 関連コードを示す
categories: [GitHub Copilot, Prompt Engineering]
weight: 5
---

**GitHub Copilot** は、**既存ファイル**のコードを**新しいファイル**で活用するときにも大変便利です。ポイントは、**関連するコードをプロンプトに含めて**「このクラスで定義したメソッドを使ってほしい」と明示すること。以下では、**`NumberUtils.java`** をあらかじめ用意してから、新しいクラス `ArrayMultiplier` を生成する例を紹介します。

---

### :pen: 例題

#### 既存ファイル `NumberUtils.java`

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

ここで定義された **`triple`** や **`square`** などのメソッドを**新ファイル**でも呼び出したい場合、プロンプトで「**#file:NumberUtils.java**」と指定することで、明示的に関連ファイルを示すことができます。

#### プロンプト例

```text
#file:NumberUtils.java

要件:
- 新しいクラス名: ArrayMultiplier
- メソッド: multiplyArrayByTriple(int[] arr)
- 戻り値: int[] (配列内の値をNumberUtils.tripleで3倍にして返す)
- 言語: Java 17
```

Copilot は既存 `NumberUtils.java` を踏まえて、**`triple`** メソッドを呼び出すコードを提案してくれるはずです。

---

### :robot: 出力例

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

Copilot が **`NumberUtils.triple(...)`** を呼び出すロジックを自動生成。既存ファイルの関数を流用します。

---

### :bulb: Attach Context ボタンでの参照ファイルの明示
チャットの入力欄にある、ファイル添付ボタンを使うと、**関連ファイル**を明示的に指定できます。これにより、Copilot が**既存メソッド**を呼び出すロジックを**自然に補完**してくれるでしょう。

**VS Code:**
![Image](https://github.com/user-attachments/assets/91793791-056a-4481-8fde-b0af74ec8b9a)

**JetBrains IDE:**
![Image](https://github.com/user-attachments/assets/f4ca95c1-1e7a-4cca-a269-d27e59bae3f2)

---

### :memo: 練習

1. **他のメソッドも使う**  
   - 追加で「squareで2乗にするメソッド」や「sumを使った計算」を取り入れてみる  
2. **複数ファイルを参照**  
   - 他のファイルも参照するように指定し、Copilot が**関連コード**を活かした提案をするか確認する

---

## まとめ

- **関連コードを示す**(既存ファイルを開く、もしくは `#file:xxx` と指定) → Copilot が **既存メソッド** を呼び出すロジックを**自然に補完**  
- 大規模プロジェクトでも、**ピン留め**や**隣接タブ**を使い、**関連ファイル**を常に開いておけば Copilot が**的確な補完**を行いやすい  

こうして**既存のコード**を活かして**新ファイル**を作る際も、Copilot が**差分部分**を自動的に埋め、**効率的**に開発を進められます。