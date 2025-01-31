---
title: ⑦ コードのリファクタリング
categories: [技術者向け, GitHub Copilot 応用]
weight: 8
---

**GitHub Copilot** を使って、**既存コードをリファクタリング**（可読性や保守性、効率の向上）する方法を紹介します。ここでは **Java** の例を取り上げ、**変数名をわかりやすく変更**したり、**重複処理をメソッド化**する基本的なリファクタリング作業をどのように指示するか見ていきます。

---

### :pen: 例題1

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

**問題点**: 変数名が `a1, b1, a2, b2, c` となっており、**可読性**が低い。

#### プロンプト例

```text
変数名をわかりやすいものに変えてください。
```

### :robot: 出力例

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

Copilot が**変数名を自然に変更**し、よりわかりやすいコードへリファクタしてくれます。

---

### :pen: 例題2 – 重複コードをメソッド化

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

**問題点**: 「足し算して結果を出力」という重複した記述が散らばっている。

#### プロンプト例

```text
この足し算処理をメソッド化して、コードをシンプルにしてください。
```

### :robot: 出力例

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

Copilot が**メソッドを抽出**して重複をまとめ、可読性と保守性を向上させています。

---

### :memo: 練習

1. **追加アイデアの依頼**  
   - Copilot に「他にも改善点はある？」と聞く → さらなるリファクタ案が返ってくる場合あり

2. **ステップを分けて指示**  
   - 変数名だけ直す → さらに「重複処理もまとめて」など、**段階的**に依頼  
   - Copilot を混乱させにくく、精度が高まる

3. **他言語のコードでも試す**  
   - C#やJavaScriptなど別言語のコードを選択し、「リファクタリングして」と頼む  
   - Copilot がどの程度対応できるか確認

4. **ショートカットコマンドの活用**  
   - `/fix` を使用して、**リファクタリング**の指示を簡潔に行う

---

## まとめ

- **変数名の変更**や**重複処理の共通化**など、基本的なリファクタ作業を**Copilot** に一言で頼める  
- **「他に何か直せる？」** と追加指示すれば、更なるリファクタ案が返ってくる可能性も  
- **コードが大きい場合**は小分けにリファクタ → **ビルド＆テスト** で安全に進める  

このように **Copilot** をリファクタリング支援に活用すれば、**可読性**や**保守性**を向上しつつ、**作業効率**を上げられます。