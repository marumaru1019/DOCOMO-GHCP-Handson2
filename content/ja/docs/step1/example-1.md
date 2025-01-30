---
title: ① コード補完
categories: [技術者向け, GitHub Copilot 基本]
tags: [json, csv, Format]
weight: 1
---

GitHub Copilot の最も基本的な使用法のひとつが**コード補完**です。途中まで書いたコードに対して候補を提案してくれるため、最小限のタイプで実装が可能になります。ここでは **Java** を使ったサンプルを示しながら、**複数候補の確認**や**部分的な提案の受け入れ**を含めて解説します。

---

## :pen: 例題

1. **Java クラスとメソッド**  
   まず、メソッド本体をコメントだけにして止めてみます。

   ```java
   public class CalculationExample {
       public static int calculateSum(int a, int b) {
         // ここで実装を中断
       }
   }
   ```
   これで入力を中断すると、Copilot が残りの実装を提案してくれるはずです。

2. **提案の受け入れ** (VS Code と JetBrains IDEs で同様の操作)
   - **Tabキー** で提案を承認  
   - **Escキー** で却下

---

## :robot: 出力例

```java
public class CalculationExample {
    public static int calculateSum(int a, int b) {
        return a + b;
    }
}
```

![Image](https://github.com/user-attachments/assets/7a1d1810-fc53-4e94-a789-0067efed9d58)

---

## 複数の候補を表示する

入力内容によっては、Copilot が**複数の候補**を提示することがあります(VS Code と JetBrains IDEs で同様の操作)。  
- Windows/Linux: **Alt + ]** (次の候補)、**Alt + [** (前の候補)  
- macOS: **Option + ]** (次の候補)、**Option + [** (前の候補)

たとえば「int sum = a + b;」以外にも「int result = a + b;」など変数名の異なるパターンが候補に出ることがあります。好きな案を選んでTabキーで受け入れる、必要なければEscキーでスキップしましょう。

---

## 部分的な提案の受け入れ

GitHub Copilot が複数行をまとめて提案した場合、「全部はいらないけど、一部だけ取り込みたい」ことがあります。そこで**部分的な受け入れ**が可能です。

1. **次の単語だけ承諾**  
   - VS Code の場合、マウスを候補上に置くと「Accept Word」というボタンが表示される
   - JetBrains IDEs の場合、提案箇所の末尾で右クリックすると「Accept Next Word」というボタンが表示される
   - Windows/Linux なら **Ctrl + →**、macOS なら **Cmd + →** などのショートカットで行うこともできる

   **VS Code の場合**
   ![Image](https://github.com/user-attachments/assets/f0caf83c-d0e8-4543-99e4-ad9e772c9fe5)

   **JetBrains IDEs の場合**
   ![Image](https://github.com/user-attachments/assets/1f7c7671-0b70-4277-a1d9-421d7c988c70)
   
2. **次の行だけ承諾**  
   - VS Code の場合、マウスを候補上に置くと「Accept Line」というボタンが表示される  
   - JetBrains IDEs の場合、提案箇所の末尾で右クリックすると「Accept Next Line」というボタンが表示される
   - VS Code の場合、`editor.action.inlineSuggest.acceptNextLine` にカスタムのキーを割り当てると「この行だけ承諾」できる
   - JetBrains IDEs の場合、macOS では **Command + Control + →**、Windows では **Control + Alt + →** で「次の行だけ承諾」できる

   **VS Code の場合**
   ![Image](https://github.com/user-attachments/assets/f8c85e30-e66e-4b3c-8cdf-a02f70fd8ab7)
   **JetBrains IDEs の場合**
   ![Image](https://github.com/user-attachments/assets/b78a1dd6-86fb-4b3f-99bf-a607d7c50d06)


---

## :memo: 練習


1. **複数候補を確かめる**  
   Alt + ] / Alt + [ で前後の候補を比較し、好きなものを受け入れてみる。  
   変数名が微妙なら却下して次に進むなど、試行してみてください。

2. **部分的承諾**  
   ログ出力や不要な初期化コードを含む提案が出た場合、一部だけを承諾・残りをスキップしてみましょう。

3. **候補の一括表示機能**
   `Ctrl + Enter` をすると、複数候補が出ることを確認してみましょう。

---

## まとめ

- **途中までコードを書く** → Copilot が残りを推測して補完  
- **複数の候補**があるときは、前後を切り替えてベストなものを選ぶ  
- 必要に応じて**部分的に承諾**して不要行を飛ばせる  
