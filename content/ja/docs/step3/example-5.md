---
title: ⑤コメントだけを翻訳してコードに挿入
categories: [GitHub Copilot, Engineer Usecases]
weight: 5
---

海外から入手したコードなどで、**コメントが英語**になっている場合に「コメント部分のみを日本語に変えてほしい」ケースがあります。**Copilot** を使えば、「コメントを日本語に翻訳して」と頼むだけで**コメント欄のみ**の翻訳を自動生成し、そのまま**コードに挿入**してくれます。ここでは**インラインチャット**を中心にした手順を解説します。

---

## 1. 英語のコメントがあるコード

以下のように**Javaコード**だけど、コメントがすべて英語のサンプルを用意します。

```java
package com.example;

// This class provides an example of insertion sort
public class InsertionSort {
    // Inserts each element into the correct position in the array
    public static void sort(int[] arr) {
        // Loop from the second element
        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;

            // Move elements greater than key to one position ahead
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = key;
        }
    }

    // Main method to test the insertion sort
    public static void main(String[] args) {
        int[] array = {7, 3, 1, 5, 2};
        sort(array);
        // Print the sorted array
        for (int num : array) {
            System.out.print(num + " ");
        }
    }
}
```

**英語コメントの例**: "This class provides an example of insertion sort" など。ここだけを**日本語に翻訳**して上書きしたい。

---

## 2. インラインチャットで「コメントアウトをすべて日本語訳して」


### :pen: プロンプト例

```text
コメントアウトをすべて日本語訳して、コード自体は変えずにコメント部分だけ修正してください。
```

---

## :robot: 出力例（イメージ）

Copilot から提案される**コード全体**（コメントのみ日本語訳）の例:

```java
package com.example;

// このクラスは挿入ソートの例を示します
public class InsertionSort {
    // 配列内の各要素を正しい位置に挿入します
    public static void sort(int[] arr) {
        // 2番目の要素からループ開始
        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;

            // keyより大きい要素を1つ前に移動
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = key;
        }
    }

    // メインメソッドで挿入ソートをテスト
    public static void main(String[] args) {
        int[] array = {7, 3, 1, 5, 2};
        sort(array);
        // ソートされた配列を出力
        for (int num : array) {
            System.out.print(num + " ");
        }
    }
}
```

Copilot は**既存コードのコメント**だけを日本語化してくれる。  
(必要に応じて**メソッド名や変数名**はそのままに、あるいは**言語を指定**してコメントを別の言語に翻訳してもらうことも可能。)

---

## 3. 練習

1. **英語コメントを別の言語**へ翻訳  
   - 例: フランス語やスペイン語に翻訳し、「ソースコードはそのままにしてコメントだけ変える」と頼む  
2. **逆に日本語コメントを英語化**  
   - 同じプロンプト要領で、「コメントを英語に」と指示  
3. **部分選択 vs 全体選択**  
   - クラス全体を選ぶ / 特定メソッドだけ選ぶ → 出力がどう変わるか比較

---

## まとめ

- **英語コメント**が散りばめられたコードを**日本語**に翻訳したい → **インラインチャット**で「コメント部分だけ翻訳して」と依頼する  
- Copilot は**コメント部分を抽出し、日本語に変換**してコードに再挿入  
- **部分的翻訳**や**複数言語対応**にも応用可能

こうして**コメントだけ翻訳**するタスクは、Copilot に任せると**高速かつ手間なく**行えます。