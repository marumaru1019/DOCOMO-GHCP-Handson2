---
title: ➃ボトルネック箇所の修正や最適化
categories: [GitHub Copilot, Engineer Usecases]
weight: 4
---

**Copilot** を活用すると、現状のロジックに**ボトルネック箇所**がある場合、その**原因や最適化の方針**を提示してもらえます。並列処理・非同期化・Stream API の活用などの **“ヒント”** を得ながら、自分で**性能検証**を行いつつ、**提案コード**を採用すれば効率的にパフォーマンスを向上させられます。

---

## 例題 – ボトルネックを含む Java コード

以下に、**性能的に問題のある**サンプルコードを示します。  
たとえば、**複数ファイル**の行数を合計する際に、**単一スレッドで**かつ**冗長なロジック**を使って読み込んでいます。

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

### 問題点例
- **すべて単一スレッド**で処理しており、ファイル数が多い場合に遅い  
- 毎回 `ArrayList` に読み込んでから `.size()` で行数取得 → メモリ的にも非効率  
- `FileReader` → `BufferedReader` を都度生成・破棄している

---

## 1. Copilot でボトルネックを発見するヒントをもらう

### :pen: プロンプト例（チャットビュー）

```text
このJavaコードに性能的なボトルネックがあると思います。効率を上げたいので、並列処理やStream APIを使うアイデアをください。どのように最適化すればいいでしょう？
```

### :robot: Copilot の提案イメージ

Copilot が、例えば以下のような**ヒント**を返すかもしれません:
```
- ファイルを並列に読み込むために ExecutorService を使って、各ファイルの行数カウントを並列実行する
- 行数だけなら、readLine→ArrayList への追加を省略し、カウンタを回すだけで済む
- try-with-resources 構文で close 処理を簡潔に書く
- Stream API を活用して Files.lines(...) で行数をcountできる
```

---

## 2. Copilot に実装を任せる

### :pen: プロンプト例

```text
では上記のアドバイスをもとに、ExecutorService を使って複数ファイルを並列に行数カウントする方法を書いてください。
ファイル名リストを受け取り、最終的な合計行数を返すメソッドを新設してください。
言語: Java 17
```

### :robot: 出力例
```java
import java.io.*;
import java.nio.file.*;
import java.util.List;
import java.util.concurrent.*;

public class ParallelFileLineCounter {

    public static long countTotalLinesInParallel(List<String> filePaths) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());
        List<Future<Long>> futures = new java.util.ArrayList<>();

        for (String path : filePaths) {
            Callable<Long> task = () -> {
                try (BufferedReader br = Files.newBufferedReader(Paths.get(path))) {
                    long lines = 0;
                    while (br.readLine() != null) {
                        lines++;
                    }
                    return lines;
                }
            };
            futures.add(executor.submit(task));
        }

        long total = 0;
        for (Future<Long> f : futures) {
            try {
                total += f.get();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }
        executor.shutdown();
        return total;
    }
}
```
Copilot が**並列処理**や**try-with-resources**、**`Files.newBufferedReader()`** を活用した改善案を提案。

---

## 3. 自分で性能検証

Copilot が**あくまでサジェスト**ですので、**実際に動作させて**  
- 本当に速くなったか？  
- スレッド数で最適化できるか？  
- I/O がネックではないか？  

といった**パフォーマンス検証**を人間がやる必要があります。

---

## まとめ

- **ボトルネックロジック**を Copilot Chat に貼り「並列化したい」「ストリームAPIを使いたい」と相談すると、サンプル実装の提案を得られる  
- **Copilot** は「この部分をどう最適化すべき？」という**アイデア**も提示してくれることがある  
- 最終的な**性能検証やリファクタの妥当性**は手動で確認が必須

こうして**Copilot**を使いながら**パフォーマンス向上**のアイデアや実装サンプルを得ると、**エンジニアの負担**を軽減できます。