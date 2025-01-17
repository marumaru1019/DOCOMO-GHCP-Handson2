---
title: ③テスト設計に基づいた単体テストの自動生成
categories: [GitHub Copilot, Engineer Usecases]
weight: 3
---

プロジェクトで **ソートアルゴリズム**など複数のクラスに対して、**あらかじめ策定したテスト設計**（テストケース）を元に単体テストを一括生成したい、というニーズはよくあります。**Copilot** を使えば、**テスト用の設計書**を **`#file`** で読み込ませたり、**複数ファイル**を同時にコンテキストに含めることで、**効果的にテストコード**を自動生成できます。さらに **Copilot Edits**(プレビュー) を利用すると、**ファイル全体をまとめて生成**・編集していく流れもスムーズになります。

---

## 1. ソースコード – 3つのソートクラス

### BubbleSort.java

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

### QuickSort.java

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

### MergeSort.java

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

## 2. テスト設計書 (testDesign.md)

以下のマークダウンファイルは **3つのソートクラス**に共通して適用できるテストケースを示しています。

```markdown
下記の実装手順に基づいて、テストケースを設計してください。
step1: 1メソッドにつき2パターン以上の正常系のテストケースを作成
step2: 1メソッドにつき1パターン以上の異常系のテストケースを作成
step3: 1メソッドにつき1パターン以上の境界値のテストケースを作成
step4: 1メソッドにつき1パターン以上の特殊なケースのテストケースを作成
```

---

## 3. Copilot でテスト自動生成 – プロンプト例

### 1) ファイルのコンテキストアタッチ

1. **testDesign.md** を #file で指定  
2. **BubbleSort.java / QuickSort.java / MergeSort.java** を同時を **コンテキスト**に含める

```text
#file testDesign.md に基づいて参照しているすべての Java クラスに対して、テストコードを生成してください
```

### 2) Copilot Edits を用いてファイルごと生成

- **Copilot Chat パネル**等で、Copilot Edits機能を使うと「ファイルをまとめて作る」操作がスムーズ  
- 例: Copilot が**一括で** `BubbleSortTest.java` / `QuickSortTest.java` / `MergeSortTest.java` の3つを提案

---

## :robot: 出力例（イメージ）

```java
// BubbleSortTest.java
import static org.junit.Assert.*;
import org.junit.Test;
import java.util.Arrays;

public class BubbleSortTest {

    @Test
    public void testEmptyArray() {
        int[] arr = {};
        BubbleSort.sort(arr);
        assertArrayEquals(new int[]{}, arr);
    }

    @Test
    public void testSingleElement() {
        int[] arr = {5};
        BubbleSort.sort(arr);
        assertArrayEquals(new int[]{5}, arr);
    }

    // ... (same for ascending, descending, duplicates, large array)
}
```

```java
// QuickSortTest.java
import static org.junit.Assert.*;
import org.junit.Test;
import java.util.Arrays;

public class QuickSortTest {

    @Test
    public void testEmptyArray() {
        int[] arr = {};
        QuickSort.sort(arr);
        assertArrayEquals(new int[]{}, arr);
    }

    // ... (他のテストケース)
}
```

```java
// MergeSortTest.java
import static org.junit.Assert.*;
import org.junit.Test;
import java.util.Arrays;

public class MergeSortTest {

    @Test
    public void testEmptyArray() {
        int[] arr = {};
        MergeSort.sort(arr);
        assertArrayEquals(new int[]{}, arr);
    }

    // ... (他のテストケース)
}
```

Copilot は**テスト設計書**（testDesign.md）を参照し、**3つのソートクラス**に対して**網羅的なテストケース**を生成します。これにより、**テストコードの初期段階**を効率的に整備できます。

---

## 練習

1. **テスト設計書を拡張**  
   - 例: 「負の値が含まれる場合」「すべて同じ値の場合」といったケースを追加 → Copilot がどうテストを増やすか確認
2. **結合テスト的項目**  
   - 「ソート後の結果を別の関数に渡す」など、**少し複雑なフロー**を指示してみる
3. **Copilot Edits** でマルチファイル生成  
   - Chat パネルで「BubbleSortTest.java, QuickSortTest.java, MergeSortTest.java をまとめて作って」と頼む → Copilot が**一括提案**するかを試す

---

## まとめ

- **テスト設計書** (testDesign.md) に、各クラスに対応するテスト項目をまとめ → Copilot が**一括**でテストクラスを生成  
- **複数ファイル** (BubbleSort.java / QuickSort.java / MergeSort.java) を同時に参照することで、それぞれ専用のテストが作成可能  
- **Copilot Edits** 機能を使うと、**ファイルごと**をまとめて提案・生成するフローがスムーズ  
- 大量のテストを手書きせず、**最初のドラフト**を Copilot に作らせ → 必要な調整を加える流れが効率的  

これにより、**大規模なテスト作成**でも Copilot と**明確な設計書**を組み合わせることで、**高速かつ網羅的**なテストが整備しやすくなります。