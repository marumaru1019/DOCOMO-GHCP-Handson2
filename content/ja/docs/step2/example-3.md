---
title: ③段階的に指示を与える
categories: [GitHub Copilot, Prompt Engineering]
weight: 3
---

**Chain-of-Thought Prompting** では、**「このようなステップで考えてほしい」** と事前に“思考過程”を提示し、Copilot に段階的な推論を促します。ここでは、**やや複雑なロジック**を一歩ずつ説明したうえで **Java** の関数を書かせる例を見てみましょう。

---

## Chain-of-Thought Prompting

### 概要

- **複雑な処理**を「ステップ1 → ステップ2 → … → 結果」と**明示的に書く**
- Copilot は示されたステップを模倣して**論理的にコードを生成**する
- たとえば「標準偏差を計算→外れ値を除去→残りの要素の平均を返す」といった多段階のタスクに有効

---

## :pen: 例 – 外れ値を除去して平均を算出 (CoT活用)

以下のような **Chain-of-Thought** (思考過程) を含むプロンプトをチャットビューで与えます。

### プロンプト例

```text
次のステップを踏むJavaの関数を作ってください (Java 17):

Chain-of-Thought (推論手順):
1) 入力は int[] (整数配列)
2) まず配列の平均値 (mean) を計算
3) 次に標準偏差 (stdev) を計算
4) その後、要素のうち "mean + 2*stdev" を超えるものを外れ値として除去
5) 最後に、外れ値除去後の要素だけで平均を計算して返す

戻り値: double (最終的な平均値)
関数名: computeFilteredAverage
```

#### ポイント

1. **ステップ順**をはっきり書く (Chain-of-Thought)  
2. **引数型** (int[]), **戻り値** (double), **関数名** (computeFilteredAverage) など明記  
3. 「Java 17」など**言語・バージョン**を指定

---

### :robot: 出力例

```java
public class StatisticsUtils {

    public static double computeFilteredAverage(int[] values) {
        // 1) 平均を計算
        double mean = 0;
        for (int v : values) {
            mean += v;
        }
        mean /= values.length;

        // 2) 標準偏差を計算
        double sumSq = 0;
        for (int v : values) {
            double diff = v - mean;
            sumSq += diff * diff;
        }
        double variance = sumSq / values.length;
        double stdev = Math.sqrt(variance);

        // 3) 外れ値を除去 (mean + 2*stdev より大きい要素)
        java.util.List<Integer> filtered = new java.util.ArrayList<>();
        double threshold = mean + 2 * stdev;
        for (int v : values) {
            if (v <= threshold) {
                filtered.add(v);
            }
        }

        // 4) 残り要素の平均を計算
        if (filtered.isEmpty()) {
            return 0.0; // すべて外れ値なら0 (or any fallback)
        }
        double sum = 0;
        for (int f : filtered) {
            sum += f;
        }
        return sum / filtered.size();
    }
}
```

Copilot は「**ステップ1 → 2 → 3 → 4 → 5**」という **Chain-of-Thought** をもとに、それぞれの計算を順番に実装してくれる可能性が高いです。

---

## 練習

1. **ステップを増やす or 変更する**  
   - 例: 「外れ値除去後に**最小値・最大値**も算出して戻す」など、追加の処理をステップ4.5 的に挿入して再依頼  
   - Copilot がどうコードを再提案するか観察

2. **ゼロショットとの比較**  
   - 同じタスクを「ゼロショット」で頼む（チェーン・オブ・ソートを何も書かない） → 生成コードとの差を比べる

3. **英語/日本語の混在**  
   - CoTステップを英語で書く / 日本語で書く → Copilot の対応を見て、どちらが作業しやすいか試す

---

## まとめ

- **Chain-of-Thought**: コードロジックを**段階的に指示**し、Copilot に推論工程を暗示  
- **複雑なロジック**や**複数ステップ**が必要なコードで有効  
- 大まかな処理を「ステップ1→ステップ2→…」と明確に書いて、「どのフェーズで何をするか」をCopilotに理解させる  

これにより、**高度な推論**が要求される場合でも、Copilot が**ミスを減らし**コードを生成してくれる確率が高まります。