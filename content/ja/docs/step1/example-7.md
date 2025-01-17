---
title: ⑦コードの翻訳
categories: [技術者向け, GitHub Copilot 応用]
weight: 7
---

GitHub Copilot は、**異なる言語のコードを翻訳**する際にも活用できます。ここでは、**Python のコードを Java に変換**する方法を、短いサンプルスクリプトを通じて紹介します。

---

## コードの翻訳 – Python → Java

1. **Python のコードを用意する**  
   たとえば、2つの数を足して2乗を返す、シンプルな関数を Python で書いてみましょう。
   ```python
   # add_and_square.py
   def add_and_square(x, y):
       """
       Returns (x + y)^2.
       """
       sum_value = x + y
       return sum_value * sum_value
   ```
   このコードを実行すると、たとえば `add_and_square(3, 4) = 49`。

2. **Copilot に「Java で書き直して」と頼む**  
   - チャットビューで、Python コードを選択
   - 「この Python を Java に翻訳して」と依頼  
   - 必要なら「JavaDocコメントも追加して」と要望を加える

3. **Copilot が出力例を提示**（イメージ）

   ```java
   public class AddAndSquare {

       /**
        * Returns (x + y)^2 in Java.
        */
       public static int addAndSquare(int x, int y) {
           int sumValue = x + y;
           return sumValue * sumValue;
       }

       public static void main(String[] args) {
           int result = addAndSquare(3, 4);
           System.out.println("Result = " + result); // Expect 49
       }
   }
   ```
   Copilot が自動的にクラスやメソッドの形を整え、コメントを付ける場合があります。

---

### Tips & 練習

- **ライブラリ依存コード**  
  Python では標準ライブラリを使っている箇所を、Java 版ではどのように変換するか Copilot に相談  
- **大きなコードを翻訳**  
  大規模なファイルを一度に貼り付けると、Copilot が要約して返す可能性あり → 必要に応じて分割して翻訳  
- **最終チェック**  
  生成された Java コードをビルドして、**正しく動くか**を確認。メソッド名やクラス構造を微調整しましょう。

---

**まとめ**  
- GitHub Copilot に「**このコードを別の言語で書き直して**」と頼むだけで、移植のたたき台を得られる  
- Python → Java の場合、Copilot が型やクラス構造を推測しながら提案  
- 大規模な移植の際は、**小分けに翻訳 → コンパイル & 動作確認 → 次のステップ**という流れが安全です。