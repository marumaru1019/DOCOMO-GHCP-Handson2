---
title: ② コードレビューの自動化
categories: [GitHub Copilot, Engineer Usecases]
weight: 2
---

コードレビューは、**チーム開発において不可欠なプロセス**ですが、手間がかかるため**自動化**や**半自動化**による効率化が望まれます。**GitHub Copilot** を利用すれば、事前に用意した**レビュー項目**（チェックリスト）に沿ったコメントや、インラインでのコードレビューを簡単に行えます。

---

### :pen: 例題

1. **Markdownファイル** (例: `review.md`) に**レビュー項目**を書き連ねる  
2. Copilot に **「#file review.md に沿ってコードレビューを作成して」** と依頼 → 自動生成コメントを得る  

#### `review.md` (チェックシート例)

```markdown
# Team Review Checklist

1. コードが予期せぬ動作をしないか確認する
2. 実装に創造的なアプローチが取り入れられているか評価する
3. コメントが将来のメンテナンスに役立つ情報を提供しているか確認する
```

#### レビュー対象のコード

```java
public class BubbleSort {
    public static void sort(int[] arr){
        for(int i=0;i<arr.length;i++){
            for(int j=0;j<arr.length;j++){
                if(arr[i] > arr[j]){
                    int temp=arr[i];
                    arr[i]=arr[j];
                    arr[j]=temp;
                }
            }
        }
    }
    public static void main(String[] args){
        int[] data = {5, 1, 4, 2, 8};
        sort(data);
        for(int num : data){
            System.out.println(num);
        }
    }
}
```

#### プロンプト例

```text
#file:review.md  に沿ってコードレビューコメントを作成してください
```

---

### :robot: 出力例

```plaintext
1. コードが予期せぬ動作をしないか確認する
   - バブルソートの内部ループで arr.length 全体をループしているため、典型的なバブルソートよりも効率が落ちる可能性があります。
   - バブルソートの正しいループ範囲を i, j で決める方が計算量を減らせます。

2. 実装に創造的なアプローチが取り入れられているか評価する
   - 標準的なアルゴリズムのため、特別な工夫は見受けられません。しかし単純明快で理解しやすい。

3. コメントが将来のメンテナンスに役立つ情報を提供しているか確認する
   - 現在コメントがありません。ソートの仕組みや計算量を示すコメントを入れると保守性が上がります。
```

Copilot が**チェックシート**を読み取り、対応するコメントや評価を生成してくれます。

---

### :memo: 他のレビュー支援機能

1. **インラインチャットでの部分レビュー**  
   - コードの一部を選択 → 右クリック → Copilot → "Review and Comment"  
   - 選択範囲だけレビューコメントを生成  
   ![Image](https://github.com/user-attachments/assets/569bb39b-6d4f-4ed6-ba89-350c6588e256)
2. **カスタムインストラクション**  
   - `.github/copilot-instructions.md` や VS Code 設定の `"github.copilot.chat.codeGeneration.instructions"` に**レビュー項目**を記載しておけば、常にCopilotが意識してレビューコメントを提案する

---

## まとめ

- **Markdown形式**のチェックリスト (例: `review.md`) を **`#file`** コマンドで参照 → Copilot が**自動レビューコメント**を生成  
- インラインで部分的にレビューするときは「**Review and Comment**」を使う  
- **カスタムインストラクション**（プレビュー機能）を使えば、**レビュー方針**をプロジェクト全体で共有・適用しやすい  

こうして**事前に決めたレビュー項目**をCopilotに取り込むと、**コードレビュー**の効率が大幅に上がり、**チーム品質**を高めることができます。