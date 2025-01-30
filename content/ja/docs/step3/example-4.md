---
title: ④ ボトルネック箇所の修正や最適化
categories: [GitHub Copilot, Engineer Usecases]
weight: 4
---

大規模なコードや複数ファイルを扱うプロジェクトでは、**ボトルネック箇所**が見つかると大幅なパフォーマンス低下を招きます。**GitHub Copilot** を利用すれば、その**原因のヒント**や**並列処理・非同期化**などの**最適化サンプル**を得られます。ただし、最終的には自分で**性能検証**を行い、**提案コード**が実際に効果を発揮するかを確認することが重要です。

---

### :pen: 例題 – パフォーマンスに問題のあるサンプルコード

以下は、**多数のファイルを1つずつ読み込み、行数を合計**している Java コードです。  
単一スレッド + 冗長ロジックによる**ボトルネック**が考えられます。

```java
package com.example;

import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class FileLineCounter {

    public static long countTotalLines(List<String> filePaths) {
        long totalLines = 0;

        // 非効率: 毎回 new FileReader -> new BufferedReader
        // 読み込みごとに無駄な処理が多い
        for (String path : filePaths) {
            try {
                FileReader fr = new FileReader(path);
                BufferedReader br = new BufferedReader(fr);
                // 無駄に ArrayList に行を貯めてから数える
                List<String> lines = new ArrayList<>();
                String line;
                while ((line = br.readLine()) != null) {
                    lines.add(line);
                }
                totalLines += lines.size();
                br.close();
                fr.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        return totalLines;
    }

    public static void main(String[] args) {
        // 大量のファイルを一度に読み込む想定
        List<String> files = new ArrayList<>();
        files.add("bigFile1.txt");
        files.add("bigFile2.txt");
        // ... (さらに多数)

        long result = countTotalLines(files);
        System.out.println("Total lines: " + result);
    }
}
```

**主な問題点**  
- **全て単一スレッド**で処理しており、ファイル数が多いと遅くなる  
- 毎回リストに行をためて `.size()` を取るだけ → メモリが無駄に消費  
- `FileReader` + `BufferedReader` を都度作って閉じる → 開閉回数が多くなる  

---

#### プロンプト例

```text
このJavaコードが遅いので、並列処理やStream APIを使った最適化案をください。
ExecutorServiceなどを使って複数ファイルを同時に行数カウントしたいです。
```

### :robot: 出力例 – Copilot による最適化サンプル

Copilot は以下のような**並列処理**を用いた書き直しを提案してくれるかもしれません。

```java
import java.io.*;
import java.nio.file.*;
import java.util.List;
import java.util.concurrent.*;

public class ParallelFileLineCounter {

    public static long countTotalLinesInParallel(List<String> filePaths) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(
            Runtime.getRuntime().availableProcessors()
        );
        List<Future<Long>> futures = new java.util.ArrayList<>();

        for (String path : filePaths) {
            Callable<Long> task = () -> {
                try (BufferedReader br = Files.newBufferedReader(Paths.get(path))) {
                    long lines = 0;
                    while (br.readLine() != null) {
                        lines++;
                    }
                    return lines;
                } catch (IOException e) {
                    e.printStackTrace();
                    return 0L;
                }
            };
            futures.add(executor.submit(task));
        }

        long total = 0;
        for (Future<Long> f : futures) {
            try {
                total += f.get(); // merge partial results
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }

        executor.shutdown();
        return total;
    }
}
```

Copilot の提案では、**ExecutorService** を使い **複数ファイル**を**並列**で読み込み、**stream** か**BufferedReader** で行数を数えています。  
メモリも最小限で済む上、**I/O待ち**を並列化できるため、高速化が期待できます。

---

### :memo: 練習

1. **改良後のコードをビルド＆実行**  
   - 実際にファイル多数を読み込んで**処理速度**を測定し、**単一スレッド版との違い**を確認  
2. **更なる最適化を依頼**  
   - 「ファイルが大きいときにはバッファサイズを増やしてほしい」「Stream APIを全面活用して」と追加指示  
3. **制限や注意点**  
   - 大量スレッドを作り過ぎると逆に遅くなる → Copilotの提案を**鵜呑み**にせず、**人間が検証**して最適な構成を見つける  

---

## まとめ

- **性能面に問題のあるロジック**を Copilot Chat に貼り付け、「並列化したい」「余計なリスト化を省きたい」と尋ねると、**サンプル実装**や**ヒント**が得られる  
- **提案**を適用後は **ビルド＆性能検証** で速度アップを確認 → 必要なら「さらに最適化して」と追加指示  
- Copilot はあくまで**補佐** → **最終的な正しさ**（レースコンディションの有無、I/O制約など）は人間がチェックして完成度を高める  

こうした**Copilot**の活用により、**ボトルネックロジック**の**改善案**や**並列化のコード例**を簡単に入手でき、**パフォーマンス最適化**を迅速に行えます。