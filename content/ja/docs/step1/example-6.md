---
title: ⑥ 隣接タブ & ピン留め
categories: [技術者向け, GitHub Copilot 応用]
weight: 6
---

**GitHub Copilot** は「**現在開いているファイル＋隣接タブ**」のファイルを優先してコード提案を行う仕組みがあります。
**重要なファイル**を **ピン留め (Pin Tab)** しておけば、いつでも参照しやすくなり、Copilot の提案精度も向上します。ここでは、**Java** での例を通じて、隣接タブ＆ピン留めを活用する方法を解説します。

---

### :pen: 例題 – 隣接タブで参照コードを開く

1. **ファイル①: `Helper.java` (タブ1)**
   ```java
   public class Helper {
       public static String greet(String name) {
           return "Hello, " + name + "!";
       }
   }
   ```

2. **ファイル②: `App.java` (タブ2)**
   ```java
   public class App {
       public static void main(String[] args) {
           // Helper クラスを使って挨拶を表示
       }
   }
   ```

**ポイント**:
- **`Helper.java`** を隣のタブで開いておく → **`App.java`** にコードを書いているときに Copilot が `greet(...)` を自動提案してくれる可能性が高まる

### :robot: 出力例

```java
public class App {
    public static void main(String[] args) {
        // Helper クラスを使って挨拶を表示
        System.out.println(Helper.greet("GitHub Copilot"));
    }
}

```
Copilot は、**隣のタブ**で開かれている `Helper` クラスを参照して、自動的に `Helper.greet(...)` の呼び出しコードを提案します。

---

## ピン留め (Pin Tab)

Copilot が**参照するファイル**を管理するために、**ピン留め**機能を活用しましょう。
**ピン止め**されたファイルは**常に開いている状態**になり、Copilot はその情報を**継続的に参照**します。

### 1. ピン留めの方法

- ファイルタブを右クリック → **「Pin」** を選ぶ
    ![Image](https://github.com/user-attachments/assets/1f4f7e86-1f18-4c0c-b95d-e1f239cb3cbd)

### 2. 使い方の例

- **例**: `Helper.java` をピン留め
  - Copilot はこのファイルを**文脈**として認識し続ける → `App.java` から `Helper` のメソッド呼び出しを勝手に提案
  - 関連するファイルにもかかわらず誤って閉じてしまうことを防ぐ

![Image](https://github.com/user-attachments/assets/8dd2d696-83a8-4423-b293-83d665e505f1)

---

### :memo: 練習

1. **「Helper.java」を閉じる**
   - Copilot の提案がどの程度変化するか確認
2. **不要ファイルは閉じる**
   - Copilot が無関係ファイルを参照しないよう、必要なファイルだけを隣接orピン留め → 提案の焦点を絞る
3. **Helper クラスのメソッド名を変更**
   - `greet` から `welcome` などに変更して、Copilot が適切に対応するか確認
---

## まとめ

- **隣接タブ**: Copilot は「開いているファイル＋その隣のタブ」に注目してコード提案 → 必要クラスを同時に開くことで、**呼び出しメソッド**を自動提案
- **ピン留め (Pin Tab)**: 重要ファイルを常時開いておくことで、**Copilot** が**参照**しやすくなる
- **ポイント**: 不要ファイルを閉じ、必要ファイルだけをピン留め or 隣接タブで管理 → **提案の精度UP** & **作業効率UP**
