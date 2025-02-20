---
title: ③ テスト設計に基づいた単体テストの自動生成
categories: [GitHub Copilot, Engineer Usecases]
weight: 3
---

大規模プロジェクトや複数のクラスに対して、**あらかじめ策定したテスト設計**に沿って**単体テスト**をまとめて作りたい場合があります。**GitHub Copilot** なら、**テスト用の設計書**を `#file` で指定したり、**複数ファイル**をまとめてコンテキストに含めることで、一度に**効率よく**テストコードを自動生成可能です。**Copilot Edits**(プレビュー) を使うと、**複数のテストファイル**をまとめて提案・編集してもらえるためフローがスムーズになります。

---

### :pen: 例題

#### 1. ソースコード – 3つのソートクラス

##### BubbleSort.java
```java
package com.example;

public class BubbleSort {
    public static void sort(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = 0; j < arr.length - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
        }
    }
}
```

##### QuickSort.java
```java
package com.example;

public class QuickSort {

    public static void sort(int[] arr) {
        quickSort(arr, 0, arr.length - 1);
    }

    private static void quickSort(int[] arr, int left, int right) {
        if (left < right) {
            int pivotIndex = partition(arr, left, right);
            quickSort(arr, left, pivotIndex - 1);
            quickSort(arr, pivotIndex + 1, right);
        }
    }

    private static int partition(int[] arr, int left, int right) {
        int pivot = arr[right];
        int i = left - 1;
        for (int j = left; j < right; j++) {
            if (arr[j] < pivot) {
                i++;
                swap(arr, i, j);
            }
        }
        swap(arr, i + 1, right);
        return i + 1;
    }

    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

##### MergeSort.java
```java
package com.example;

public class MergeSort {

    public static void sort(int[] arr) {
        if (arr.length <= 1) return;
        int mid = arr.length / 2;
        int[] left = new int[mid];
        int[] right = new int[arr.length - mid];
        System.arraycopy(arr, 0, left, 0, mid);
        System.arraycopy(arr, mid, right, 0, arr.length - mid);

        sort(left);
        sort(right);
        merge(arr, left, right);
    }

    private static void merge(int[] arr, int[] left, int[] right) {
        int i = 0, j = 0, k = 0;
        while (i < left.length && j < right.length) {
            if (left[i] <= right[j]) {
                arr[k++] = left[i++];
            } else {
                arr[k++] = right[j++];
            }
        }
        while (i < left.length) {
            arr[k++] = left[i++];
        }
        while (j < right.length) {
            arr[k++] = right[j++];
        }
    }
}
```

---

#### 2. テスト設計書 (testDesign.md)

```markdown
下記の実装手順に基づいて、テストケースを設計してください。

step1: 1メソッドにつき2パターン以上の正常系のテストケースを作成
step2: 1メソッドにつき1パターン以上の異常系のテストケースを作成
step3: 1メソッドにつき1パターン以上の境界値のテストケースを作成
step4: 1メソッドにつき1パターン以上の特殊なケースのテストケースを作成
```

**BubbleSort** / **QuickSort** / **MergeSort** の3クラスがあり、それぞれに対して**複数のテストパターン**を作りたい。

---

#### 3. Copilot でテスト自動生成 – プロンプト例

- **`#file testDesign.md`** としてテスト設計を読み込む
- **3つのソートクラス**を **同時にコンテキスト**として提示

##### プロンプト例

```text
#file testDesign.md に基づいて、
BubbleSort.java / QuickSort.java / MergeSort.java のテストコードを生成してください（Java 17, JUnit使用）
```

Copilot は**testDesign.md** の指示 (正常系・異常系・境界値・特殊ケース) を踏まえ、3つのテストファイルをまとめて提案してくれる可能性が高いです。

---

### :robot: 出力例

```java
// BubbleSortTest.java
import static org.junit.Assert.*;
import org.junit.Test;
import java.util.Arrays;

public class BubbleSortTest {

    // step1: 2パターン以上の正常系テスト
    @Test
    public void testNormal_small() {
        int[] arr = {3, 1, 2};
        BubbleSort.sort(arr);
        assertArrayEquals(new int[]{1, 2, 3}, arr);
    }

    @Test
    public void testNormal_large() {
        int[] arr = {9,4,7,1,5,2};
        BubbleSort.sort(arr);
        assertArrayEquals(new int[]{1,2,4,5,7,9}, arr);
    }

    // step2: 異常系 (null等?)
    @Test(expected = NullPointerException.class)
    public void testAbnormal_nullInput() {
        BubbleSort.sort(null);
    }

    // step3: 境界値 (空配列)
    @Test
    public void testBoundary_emptyArray() {
        int[] arr = {};
        BubbleSort.sort(arr);
        assertArrayEquals(new int[]{}, arr);
    }

    // step4: 特殊ケース (全て同じ値)
    @Test
    public void testSpecial_allSame() {
        int[] arr = {5,5,5};
        BubbleSort.sort(arr);
        assertArrayEquals(new int[]{5,5,5}, arr);
    }
}
```

Copilot が同様に **QuickSortTest.java**、**MergeSortTest.java** も**まとめて**提示し、それぞれ**正常系・異常系・境界値・特殊ケース**が生成されるイメージです。

---

### :memo: 練習

1. **テスト設計書を拡張**
   - 例: 「負の値」「重複値」「極端に大きい配列」などを追記し、Copilot がどうテストを増やすか試す
2. **Copilot Edits** でまとめて生成
   - 「BubbleSortTest.java, QuickSortTest.java, MergeSortTest.javaを一度に作って」→ **マルチファイルの一括提案**が可能か確認
---

## まとめ

- **テスト設計書 (Markdown)** と**ソースファイル**を**同時にコンテキスト**に → Copilotが**各クラス**に対するテストを**網羅的**に提案
- **Copilot Edits** 機能で一度に**複数ファイル**を生成 → 大量のテストをまとめて初期整備
- 仕上がったテストは**ビルド＆実行**で検証し、必要なら**追加要望**をCopilotに再依頼し完成度を高める

このように、**テスト設計書**＋**Copilot**による**自動生成**を組み合わせれば、**プロジェクト**のテスト作業が格段にスピードアップします。