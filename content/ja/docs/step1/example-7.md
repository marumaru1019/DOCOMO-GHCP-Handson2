---
title: ⑥ コードの翻訳
categories: [技術者向け, GitHub Copilot 応用]
weight: 7
---

**GitHub Copilot** を使うと、**異なる言語のコード**を相互に翻訳する際にも効率的に作業できます。ここでは、**Python から Java** に変換する例を紹介しますが、他の言語ペアでも同様に活用できます。

---

### :pen: 例題

以下のような **Python** コードを **Java** に書き換えたいとします。

```python
# add_and_square.py
def add_and_square(x, y):
    """
    Returns (x + y)^2.
    """
    sum_value = x + y
    return sum_value * sum_value
```

#### プロンプト例

```text
このPythonコードをJavaに変換してください。
関数名は addAndSquare にして、JavaDocコメントもつけて。
```

---

### :robot: 出力例

```java
/**
 * Returns the square of the sum of x and y.
 *
 * @param x the first number
 * @param y the second number
 * @return the square of the sum of x and y
 */
public class AddAndSquare {
    public static int addAndSquare(int x, int y) {
        int sumValue = x + y;
        return sumValue * sumValue;
    }

    public static void main(String[] args) {
        // Example usage
        int result = addAndSquare(3, 4);
        System.out.println("The result is: " + result);
    }
}
```

Copilot は**Python の関数**を**Java のクラス＆メソッド形式**に変換し、**JavaDocコメント**も生成してくれます。

---

### :memo: 練習

1. **他の言語ペア**  
   - 「C#をTypeScriptに」「RubyをGoに」なども試し、どう変換されるか比較  
2. **プロジェクト特有言語の移植**  
   - プロジェクト内で使用している独自の言語を、他の言語に変換できるか確かめてみる

---

## まとめ

- **「このコードを〇〇言語に書き換えて」** と頼むだけで、移植の**たたき台**を得られる  
- **Python → Java** の場合、クラスや型付けなどを**Copilot** が推測して雛形を生成  
- **大規模な移植**は、**小分け**に翻訳 → **コンパイル＆テスト** → 安定したら次に進む、という流れが安全  

こうして **Copilot** を活用すれば、言語移植の作業時間を短縮しながら、**基本的な構造**を自動で整えることができます。