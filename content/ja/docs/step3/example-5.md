---
title: ⑤ コメントだけを翻訳してコードに挿入
categories: [GitHub Copilot, Engineer Usecases]
weight: 5
---

海外から入手したコードなどで、**コメントが英語**になっている場合に「**コメントだけ**を日本語に翻訳してほしい」というケースがあります。**Copilot** を使えば、**インラインチャット**の操作で「コメントを翻訳して」と頼むだけで、**コメント部分のみ**の翻訳を自動生成し、**コード本体は変更せず**に差し替えてくれます。

---

### :pen: 例題 – 英語コメントを日本語訳する

以下は **Java** の挿入ソートコードですが、**英語のコメント**が含まれます:

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

#### プロンプト例

```text
コメントアウトをすべて日本語に翻訳し、コードの処理はそのまま維持してください。
```

---

### :robot: 出力例

Copilot は**コメントだけを日本語化**して、コード自体は変更しない提案を返す可能性が高いです:

```java
package com.example;

// このクラスは挿入ソートの例を示します
public class InsertionSort {
    // 配列の各要素を適切な位置に挿入します
    public static void sort(int[] arr) {
        // 2番目の要素からループを開始
        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;

            // keyより大きい要素を一つ先にずらす
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = key;
        }
    }

    // 挿入ソートをテストするメインメソッド
    public static void main(String[] args) {
        int[] array = {7, 3, 1, 5, 2};
        sort(array);
        // ソートされた配列を表示
        for (int num : array) {
            System.out.print(num + " ");
        }
    }
}
```

このように、**英語のコメントを日本語に**(または逆)だけ置き換えて、**コード構造**は変えないまま生成してくれます。

---

### :memo: 練習

1. **他言語への翻訳**
   - 日本語→英語／英語→フランス語 など、同じ要領で「コメントを◯◯語に翻訳して」と指示
2. **部分選択のみ**
   - いくつかのメソッドや特定範囲を選択→ インラインチャットで「ここだけ翻訳」と依頼
3. **複数ファイル同時**
   - 複数ファイルを開いて**隣接タブ**に置き、「コメントをすべて日本語に変えて」と頼んでCopilotの反応を試す

---

## まとめ

- **英語コメント**だけを日本語（あるいは別言語）に置き換える際、**インラインチャット**で「コメントだけ翻訳」と指示 → **コード構造**は維持
- Copilot は**コメント部分**を抽出し、自動的に差し替えてくれる → 大幅に手間を削減
- 大規模プロジェクトや多言語対応でも、**コメント翻訳**をAIに任せて**高速化**できる

こうして**コメントだけを翻訳**してコードに挿入する作業も、Copilot の活用で**効率UP**を図れます。