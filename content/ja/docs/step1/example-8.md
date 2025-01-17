---
title: ⑧コードのリファクタリング
categories: [技術者向け, GitHub Copilot 応用]
weight: 8
---

**GitHub Copilot** を使って、既存のコードを**リファクタリング**（可読性・保守性・効率の向上など）する方法を紹介します。ここでは **Java** の例として、**変数名をわかりやすく変更**したり、**重複処理をメソッド化**する簡単なサンプルを取り上げます。

---

## コードのリファクタリング

### 例1: 変数名をわかりやすく

#### リファクタ前

```java
public class SalesApp {
    public static void main(String[] args) {
        int a1 = 3;   // Apple price
        int b1 = 100; // Apple count
        int c = a1 * b1;

        int a2 = 5;   // Orange price
        int b2 = 50;  // Orange count
        c += a2 * b2;

        System.out.println("Total Sales = " + c);
    }
}
```

変数名が `a1, b1, a2, b2, c` となっており、**意味が伝わりにくい**状態です。

#### プロンプト例
```text
変数名をわかりやすいものに変更してください
```

#### :robot: 出力例

```java
public class SalesApp {
    public static void main(String[] args) {
        int applePrice = 3;
        int appleCount = 100;
        int totalSales = applePrice * appleCount;

        int orangePrice = 5;
        int orangeCount = 50;
        totalSales += orangePrice * orangeCount;

        System.out.println("Total Sales = " + totalSales);
    }
}
```

Copilot が**変数名**を自然に変更し、可読性がアップ。

---

### 例2: 重複コードをメソッド化

#### リファクタ前

```java
public class Calculator {
    public static void main(String[] args) {
        int sumA = 3 + 4;
        System.out.println("Sum A = " + sumA);

        int sumB = 10 + 20;
        System.out.println("Sum B = " + sumB);

        int sumC = 50 + 100;
        System.out.println("Sum C = " + sumC);
    }
}
```

同じ構文（足し算と出力）が何度も登場しているため、**共通化**したい。

#### プロンプト例
```text
この足し算処理をメソッド化して、コードをもっとシンプルにしてください
```

#### :robot: 出力例

```java
public class Calculator {

    private static void printSum(int x, int y) {
        int sum = x + y;
        System.out.println("Sum of " + x + " and " + y + " = " + sum);
    }

    public static void main(String[] args) {
        printSum(3, 4);
        printSum(10, 20);
        printSum(50, 100);
    }
}
```

Copilot が**メソッド `printSum`** を抽出して、同じパターンをまとめてくれました。

---

## :memo: 練習

1. **追加アイデアの依頼**  
   - 変数名や重複コードを直した後、**「他にも改善点は？」** と Copilot に聞いてみましょう。`if-else`→ `switch` 化、ループ最適化などの提案が出るかもしれません。

2. **逐次指示を与える**  
   - 「変数名を変更して」 → 変更後に「次にクラス名も分かりやすくして」など、**段階的**にリファクタすると、Copilot が混乱せず的確に対応してくれます。

3. **他の言語でも試す**  
   - 同じ手順で、JavaScript や Python のコードを選択し「短くして」「メソッド化して」と頼むとどうなるか試してみてください。

---

## まとめ

- **変数名変更**や**重複処理のメソッド化**はリファクタリングの基本例ですが、Copilot が**簡単な指示**で自動的に修正案を提示してくれます。  
- さらに **「ほかに何か直せる？」** と尋ねると、**追加のリファクタリング案**を提案してくれる場合もあり、開発者の視野を広げてくれます。  
- 最終的には人間がビルド・テストを行い、**動作や仕様**を確認して完成度を高めましょう。