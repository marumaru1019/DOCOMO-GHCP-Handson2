---
title: ③ Copilot Chat
categories: [技術者向け, GitHub Copilot 基本]
weight: 4
---

**GitHub Copilot** には、**チャットビュー** と **インラインチャット** という 2 つの対話的な機能があり、**自然言語**で依頼してコードを書かせたり、修正・説明・テスト生成などを指示できます。ここでは、それぞれの呼び出し方や使い所を **例題** とともに解説します。

---

### :pen: 例題 – チャットビュー

チャットビューは、**VS Code のサイドバー**に表示される **Copilot Chat** ペインを使う方法です。  
過去のやりとり（会話履歴）を参照しながら、**大きな文脈**でコード生成やリファクタリングを行うのに適しています。

**呼び出し方法**:  
- VS Code
    - VS Code上部の検索窓右隣りにある「Copilot Chat」アイコンをクリック
- JetBrains IDE
    - 右のサイドバーにある「Copilot Chat」アイコンをクリック

    **VS Code**:
    ![Image](https://github.com/user-attachments/assets/d274ae7e-0b05-44a0-9906-6ab31faf0472)

    **JetBrains IDE**:
    ![Image](https://github.com/user-attachments/assets/c04b7c03-cecc-47cf-b24d-1fb6298419f8)

- ショートカットで呼び出す(VS Code 限定):
  - **Windows**: `Ctrl+Alt+I`  
  - **Mac**: `Cmd+Ctrl+I`



**プロンプト例**:
```text
Java 17 で
1～10の乱数を返す static メソッドを作ってください。
メソッド名: getRandomNumber
引数: なし
```
プロンプトをチャット画面の入力欄に入力し、送信すると Copilot がコードを生成してくれます。

**VS Code**:
![Image](https://github.com/user-attachments/assets/93a96839-a116-44fe-a6a8-242ea39b18fc)

**JetBrains IDE**:
![Image](https://github.com/user-attachments/assets/038dd17d-e739-4b58-a8d6-aba7d58aa8c7)

### :robot: 出力例

Copilot はチャットビューに以下のようなコードを提案するかもしれません（例）:

```java
import java.util.concurrent.ThreadLocalRandom;

public class RandomNumberExample {
    public static void main(String[] args) {
        System.out.println(getRandomNumber());
    }

    // 1～10の乱数を返す static メソッド
    public static int getRandomNumber() {
        return ThreadLocalRandom.current().nextInt(1, 11);
    }
}
```

**操作**:  コードブロック横に「挿入」ボタンがあれば、クリックするとエディタへ直接貼り付け可能。

---

### :memo: 練習

1. 「このクラスを別フレームワークで書くなら？」とCopilotに尋ねてみる
2. 「JUnitテストも生成して」と追加リクエストを出してみる

---

### :pen: 例題 – インラインチャット

インラインチャットは、**選択したコードブロック**に対して、よりフォーカスした対話を行う機能です。

**呼び出し方法**:  
- **VS Code**:
    - **Windows/Linux**: `Ctrl+I`  
    - **Mac**: `Cmd+I`
- **JetBrains IDE**:
    - **WIndows/Linux**: `Shift+Ctrl+G`
    - **Mac**: `Shift+Ctrl+I`

**プロンプト例** (選択したメソッド内にフォーカス):
例えば、以下のようなクラスがあるとします。
これに対して、sumArray メソッドのロジックを改善してほしいとリクエストします。

 ```java
public class CalculationExample {
    public static int calculate(int a, int b) {
        return a + b;
    }

    // **ここからsumArrayメソッド**
    public static int sumArray(int[] arr) {
        int sum = 0;
        for (int i : arr) {
            sum += i;
        }
        return sum;
    }
    // **sumArrayメソッド終了**
}
```

インラインチャットで以下のようなリクエストを出してみます。
```text
この部分のロジックをもう少し最適化してください。
```
![Image](https://github.com/user-attachments/assets/f098b354-cd9e-4160-a9ef-f92ed21bc0b4)

### :robot: 出力例


```java
public static int sumArray(int[] arr) {
    // 改良版： 単純にストリームで合計
    return Arrays.stream(arr).sum();
}
```

**操作**:  
- インラインチャット上で提案を確認し、必要なら`Accept`ボタンをクリックしてコードを挿入
- 不要であれば、`Discard`ボタンで提案を破棄
- 再度生成を試す場合は、ぐるぐるアイコンをクリック

![Image](https://github.com/user-attachments/assets/eeeb1091-e498-4133-a02f-dbc517217324)

---

### :bulb: コードの誤りを自動修正させたい場合
例えば上記の出力例の場合、Arrays クラスがインポートされていないため、コードが正常に動作しないという問題があります。
このような場合、エラー箇所に赤線が引かれているので、その部分にマウスでカーソルを合わせると、「Fix using Copilot」というボタンが表示されるので、それをクリックすると自動的に修正されます。
![Image](https://github.com/user-attachments/assets/c38d4be9-8d2e-4bbe-875e-d64f0b14ce70)

---

### :memo: 練習
- 他の箇所を選択し、「コメントだけ翻訳して」「このロジックにエラー処理を追加して」など試してみる  
- インラインチャットでコードの再提案を試してみて出力が変わるか確認する
---

## まとめ

- **Copilot Chat**:
  - **チャットビュー**: 大きな文脈を持った会話に向いている  
  - 履歴を活かして「コード生成 → 追加説明 → リファクタ → テスト生成」と一連の流れをスムーズに行える
- **インラインチャット**:
  - **ショートカット**で呼び出し、選択中のコードブロックにピンポイントな修正依頼  
  - 大きなやりとりが不要な場合や、さっと1箇所だけ補完させたいときに便利

- **どちらも**自然言語でリファクタ・説明・テスト生成を頼めるが、**スコープ**や**操作感**が違うので、場面に応じて使い分けると効果的
