---
title: ⑧ 単体テストの作成
categories: [技術者向け, GitHub Copilot 応用]
weight: 9
---


**GitHub Copilot** は、既存のコードをテストする際、「**このクラスの単体テストを書いて**」と簡単に頼むだけで、**JUnit** などを用いたテストコードを自動生成してくれます。ここでは **Java** の**素数判定クラス**を例に、どのように単体テストを作成していくか紹介します。

---

### :pen: 例題

以下のコードは、**素数 (prime number)** 判定を行う `PrimeChecker` クラスです。

```java
public class PrimeChecker {
    /**
     * Checks if a number is prime (greater than 1, no divisors except 1 and itself).
     */
    public boolean isPrime(int n) {
        if (n <= 1) {
            return false;
        }
        for (int i = 2; i * i <= n; i++) {
            if (n % i == 0) {
                return false;
            }
        }
        return true;
    }
}
```

#### プロンプト例

```text
この isPrime メソッドの単体テストを JUnit で書いてください。
```

---

### :robot: 出力例

```java
import static org.junit.Assert.*;
import org.junit.Test;

public class PrimeCheckerTest {

    @Test
    public void testIsPrime_2() {
        PrimeChecker pc = new PrimeChecker();
        assertTrue(pc.isPrime(2));
    }

    @Test
    public void testIsPrime_1() {
        PrimeChecker pc = new PrimeChecker();
        assertFalse(pc.isPrime(1));
    }

    @Test
    public void testIsPrime_10() {
        PrimeChecker pc = new PrimeChecker();
        assertFalse(pc.isPrime(10));
    }

    @Test
    public void testIsPrime_17() {
        PrimeChecker pc = new PrimeChecker();
        assertTrue(pc.isPrime(17));
    }
}
```

Copilot は**複数のテストケース**を提案しており、簡易的な **JUnitテストクラス** が一瞬で作成されます。

---

### :memo: 練習

1. **テストケースを増やす**  
   - Copilot に「マイナス値や大きな値もテストに加えて」と要望し、どんなケースを追加提案してくれるか試す  
2. **パッケージやimport指定**  
   - `package com.example;` が必要なら、Copilotに「パッケージ付きで書いて」と指示すると補完してくれる  
3. **他言語でも試す**  
   - JavaScriptやPythonでも「この関数をテストして」と言う → それぞれのテストフレームワークの雛形を作成するか確認
4. **スラッシュコマンドの活用**  
   - `/test` でテストコードの作成を要求 → **JUnit** などのテストフレームワークに対応しているか確認

---

## まとめ

- **Copilot** に「単体テストを生成して」と頼むだけで、**テストクラス**を簡単に作成  
- 必要に応じて「パラメータ化テスト」「境界値テスト」「例外テスト」などを追加要求し、テストの網羅性を上げる  
- **生成されたテストをビルド & 実行**して、結果が期待通りになっているか確認  

こうして**単体テストの作成**でも、Copilot がベースコードを作ってくれるため、**開発効率**と**テストの初動スピード**が向上します。