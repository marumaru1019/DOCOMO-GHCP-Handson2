---
title: ⑧ エラーログからエラーを解消する
categories: [GitHub Copilot, トラブルシュート]
weight: 8
---


## 導入部
本記事では、シンプルな計算処理を行うJavaクラスを例に、割り算で発生する **ArithmeticException** の原因をターミナルログを **#terminalSelection** でCopilotに渡し、エラー修正案を自動生成する手法を解説します。直感的な例として、ゼロ除算によるエラーの発生とその対処方法を学びます。

---

### :pen: 例題

#### エラーのあるJavaコード例
以下のコードは、二つの整数を割り算する際に、分母がゼロの場合に **ArithmeticException** を引き起こします。

```java
public class SimpleCalculator {

    /**
     * 二つの整数を割り算するメソッド。
     * 分母がゼロの場合、ArithmeticExceptionが発生する。
     */
    public static int divideNumbers(int numerator, int denominator) {
        return numerator / denominator;
    }

    public static void main(String[] args) {
        int a = 10;
        int b = 0; // 分母がゼロのためエラーが発生
        int result = divideNumbers(a, b);
        System.out.println("Result: " + result);
    }
}
```

#### ターミナルログ例
実行時に以下のエラーログが出力されます。

```plaintext
Exception in thread "main" java.lang.ArithmeticException: / by zero
        at SimpleCalculator.divideNumbers(SimpleCalculator.java:8)
        at SimpleCalculator.main(SimpleCalculator.java:14)
```

#### Copilotへのエラー解消依頼プロンプト
上記ターミナルログを **#terminalSelection** を使ってCopilotに渡し、エラー修正を依頼します。

```text
#terminalSelection
エラーログを見てコードを修正して下さい。
```
<img width="1682" alt="Image" src="https://github.com/user-attachments/assets/7171afe1-df84-415b-8d1b-c09c902b08fb" />
---

### :robot: 出力例

Copilot により生成される修正後のコード例は以下の通りです。
この修正では、分母がゼロの場合にエラーメッセージを出力し、計算を中断する処理を追加しています。

```java
public class SimpleCalculator {

    /**
     * 二つの整数を割り算するメソッド。
     * 分母がゼロの場合はエラーメッセージを出力し、0を返す。
     */
    public static int divideNumbers(int numerator, int denominator) {
        if (denominator == 0) {
            System.err.println("Error: Denominator is zero. Division cannot be performed.");
            return 0;
        }
        return numerator / denominator;
    }

    public static void main(String[] args) {
        int a = 10;
        int b = 0; // 分母がゼロの場合はエラー回避のため、エラーメッセージを出力して0を返す
        int result = divideNumbers(a, b);
        System.out.println("Result: " + result);
    }
}
```

---

### :memo: 練習

すぐに試せる以下の実践的な練習を行ってみましょう。

1. **例外をスローする実装に変更**
   - `divideNumbers` メソッド内で、分母がゼロの場合にエラーメッセージを出力する代わりに、`IllegalArgumentException` をスローするように変更してみましょう。
   - 変更後のコードをコンパイル・実行し、例外がスローされることを確認してください。

2. **ユーザー入力による動的な値のテスト**
   - `main` メソッドに `java.util.Scanner` を追加して、ユーザーから分子と分母の値を入力できるように変更しましょう。
   - 入力値がゼロの場合に、適切なエラーメッセージや例外処理が行われるか、すぐに動作を確認してみてください。


---

## まとめ

- **#terminalSelection** を活用して、実際のターミナルログからエラー原因を正確に伝えることで、具体的な修正案を自動生成できます。
- 修正後のコード例では、分母がゼロの場合の処理を追加し、**ArithmeticException** の発生を未然に防ぐ対策が実装されています。
- 今回の練習では、すぐに実行・確認できる修正依頼とユーザー入力を使ったテストを行い、Copilot の提案精度や運用方法を実践的に理解できます。
- この手法により、実行ログから迅速にエラー原因を把握し、シンプルかつ直感的なエラー解消が可能となります。
