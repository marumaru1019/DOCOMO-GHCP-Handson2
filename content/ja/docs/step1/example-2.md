---
title: ②コメントからコードを生成
categories: [技術者向け, GitHub Copilot 基本]
weight: 2
---


GitHub Copilot では、**コメントを使ってコードを生成**するというテクニックも非常に有効です。  
「どのようなメソッド名を使うか」「引数や戻り値はどうなるか」を、コメントで簡潔に指定するだけで、残りの実装を推測して提案してくれます。  

ここでは Java をベースとし、**単数行コメント**・**複数行コメント**をそれぞれ使ってコードを生成する例を紹介します。

---

## :pen: 例題

### 1. 単数行コメントで指示する
1. **途中までコードを書く**  
   - まずはクラスとメソッドの外側を用意します。
   ```java
   public class AverageCalculationExample {
       // Function name: calculateAverage
       // Function arguments: numbers (array)
       // Return type of the function: number (in Java, use double or int as needed)
   }
   ```
   ここで敢えて中身を書かず、**コメントに「関数名・引数・戻り値」** だけを指定して止めておきます。  

2. **Copilot が提案するコード**  
   - Copilot はコメントを参照し、以下のようなメソッドを自動提案するかもしれません。

   ```java
   public static double calculateAverage(int[] numbers) {
       int sum = 0;
       for (int num : numbers) {
           sum += num;
       }
       return (double) sum / numbers.length;
   }
   ```

3. **Tabキー** で受け入れると、メソッドが完成します。

---

### 2. 複数行コメントで指示する
コメントを複数行に分けて、より詳細に要件を書くことも可能です。  
```java
public class AverageCalculationExample {
    /*
        Function name: calculateAverage
        Function arguments: numbers (array of ints)
        Return type: double
        Behavior:
          - sum up all elements
          - divide by length
          - handle empty array by returning 0
    */
}
```
これだけ書いて数秒待つと、Copilot が上記のコメントに従って処理を提案します。  
- 例: 空配列なら 0.0 を返す  
- 例: 通常時は合計をlengthで割った値を返す

---

## :robot: Copilot による出力例

```java
public class AverageCalculationExample {
    /*
        Function name: calculateAverage
        Function arguments: numbers (array of ints)
        Return type: double
        Behavior:
          - sum up all elements
          - divide by length
          - handle empty array by returning 0
    */
    public static double calculateAverage(int[] numbers) {
        if (numbers == null || numbers.length == 0) {
            return 0.0;
        }
        int sum = 0;
        for (int num : numbers) {
            sum += num;
        }
        return (double) sum / numbers.length;
    }
}
```
Copilot がコメントをもとに、しっかり空配列対応も入れたコードを生成してくれるかもしれません。

---

## :memo: 練習

1. **最大値を計算する関数**  
   - 以下のコメントを記述し、Copilot がどんな実装を提案するか試してみましょう。
   ```java
   // Function name: calculateMax
   // Function arguments: numbers (array)
   // Return type: int
   ```
   - うまくいけば `calculateMax` が生成され、配列内の最大値を返すメソッドが完成するはずです。

2. **異なる型や要件を付け加える**  
   - 例: “空の場合は-1を返す” と書くとどうなるか  
   - 例: “引数がnullの場合、例外をスローする” と書くとどうなるか

3. **複数行コメント vs 単数行コメント**  
   - 同じ要件を、単数行コメントで書いた場合と複数行コメントで書いた場合とで結果が変わるか観察してみてください。

---

## まとめ

- **コメントだけで関数名・引数・戻り値などを指定**し、Copilot に中身を生成させるのは非常に効率的な方法です。  
- **細かい要件**（空配列時の挙動など）をコメントに書くと、Copilot が取り入れて実装してくれることがあります。  
- コメントの粒度や書き方を変えることで、提案結果が変化するので、いろいろ試して最適な書き方を見つけましょう。
