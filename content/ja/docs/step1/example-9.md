---
title: ➈単体テストの作成
categories: [技術者向け, GitHub Copilot 応用]
weight: 9
---

**GitHub Copilot** では、既存コードをテストする際に「**このクラスの単体テストを作って**」と依頼するだけで、**Java** のテストフレームワーク（例: JUnit）を使った雛形を自動生成してくれます。ここでは **素数判定クラス**を例にして、**単体テスト作成**の流れを見ていきましょう。

---

## 単体テストの作成

### 概要
- Copilot に「**このメソッドのテストを書いて**」と指示し、テスト対象クラスやメソッドを選択すると、**JUnitテストコード**を提案してくれる  
- さらに「**複数のテストケースを追加して**」など補足指示すれば、境界値や例外処理のテストも自動生成できる  

---

### :pen: 例題 – Java の素数判定クラス

#### テスト対象: `PrimeChecker.java`
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
引数 `n` が 2以上かつ割り切れる数がない場合に `true` を返すメソッド。

---

### プロンプト例

```text
この isPrime メソッドの単体テストを JUnit で書いてください
```

#### :robot: 出力例

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

Copilot が自動で**テストクラス**を作成し、**いくつかのテストケース**を含めてくれる可能性があります。

---

## :memo: 練習

1. **テストケースを増やす**  
   - Copilot に「マイナス値や大きめの値もテストして」と要望  
   - Parametrizedテスト（`@RunWith(Parameterized.class)`）などを提案する場合もある
2. **パッケージ構造を考慮**  
   - もし `com.example.PrimeChecker` のようにパッケージを指定していれば、Copilot が自動で適切な `import` や `package` 文をつけてくれるか試してみましょう
3. **テスト後に「他に書き換えられるところある？」**と尋ねる  
   - Copilot が `PrimeChecker` のロジックをさらにリファクタリングしてくれる提案を出す場合も

---

## まとめ

- **「単体テストを作って」** という一言で、Copilot が JUnit テストを生成  
- **追加ケース** や **パラメータ化** などの要望を出すと、さらに内容を充実させられる  
- 仕上がったテストは必ず **ビルド & 実行** し、 **期待通りに動く** かをチェックしてください  

このように、 **単体テストの作成** でも Copilot がアイデア出しや作業の自動化に大いに貢献してくれます。ぜひ活用してみましょう。