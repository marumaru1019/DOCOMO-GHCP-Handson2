---
title: ① コード補完
categories: [技術者向け, GitHub Copilot 基本]
tags: [json, csv, Format]
weight: 1
---

GitHub Copilot の最も基本的な使用法のひとつが**コード補完**です。途中まで書いたコードに対して候補を提案してくれるため、最小限のタイプで実装が可能になります。ここでは **JavaScript** を使ったサンプルを示しながら、**複数候補の確認**や**部分的な提案の受け入れ**を含めて解説します。

---

## :pen: 例題1. 基本的なコード補完

まず、関数本体をコメントだけにして止めてみます。

```javascript
function calculateSum(a, b) {
   // ここで実装を中断
}
```

これで入力を中断すると、Copilot が残りの実装を**ゴーストテキスト**（薄いグレーのテキスト）で提案してくれます。

**提案の受け入れ**
- **Tabキー** で提案を承認
- **Escキー** で却下
- **部分受け入れ**: Mac: `⌘→` / Windows/Linux: `Ctrl+→` で次の単語だけを承認

##  :robot: 出力例

```javascript
function calculateSum(a, b) {
   return a + b;
}
```

![補完結果イメージ](../images/code_suggestion.png)

---

##  :pen: 例題2. コメント駆動でのコード生成

コメントを使って、具体的な実装を指示できます。

```javascript
// JavaScript で Student クラスを作成
// プロパティ: name, age, grade
// メソッド: getInfo() - 学生情報を文字列で返す
// メソッド: isAdult() - 18歳以上かどうかを返す
```

このようなコメントを書くと、Copilot が意図に沿ったクラス構造を提案してくれます。

##  :robot: 出力例

```javascript
// JavaScript で Student クラスを作成
// プロパティ: name, age, grade
// メソッド: getInfo() - 学生情報を文字列で返す
// メソッド: isAdult() - 18歳以上かどうかを返す
class Student {
   constructor(name, age, grade) {
      this.name = name;
      this.age = age;
      this.grade = grade;
   }

   getInfo() {
      return `Name: ${this.name}, Age: ${this.age}, Grade: ${this.grade}`;
   }

   isAdult() {
      return this.age >= 18;
   }
}

// 利用例
const s = new Student("Taro", 19, "A");
console.log(s.getInfo());
console.log("isAdult:", s.isAdult());
```

---


## 複数の候補を表示する

入力内容によっては、Copilot が**複数の候補**を提示することがあります。

![複数候補](../images/multi_suggest.png)

- Windows/Linux: **Alt + ]** (次の候補)、**Alt + [** (前の候補)
- macOS: **Option + ]** (次の候補)、**Option + [** (前の候補)

たとえば「return a + b」以外にも「sum = a + b; return sum」など変数を使った異なるパターンが候補に出ることがあります。好きな案を選んでTabキーで受け入れる、必要なければEscキーでスキップしましょう。

---

## 部分的な提案の受け入れ

GitHub Copilot が複数行をまとめて提案した場合、「全部はいらないけど、一部だけ取り込みたい」ことがあります。そこで**部分的な受け入れ**が可能です。

![部分的提案の受け入れ](../images/accept_part.png)

1. **次の単語だけ承諾**
   - マウスを候補上に置くと「Accept Word」というボタンが表示されます。
   - Windows/Linuxでは **Ctrl + →**、macOSでは **Cmd + →** のショートカットも利用できます。

2. **次の行だけ承諾**
   - マウスを候補上に置くと「Accept Line」というボタンが表示されます。
   - `editor.action.inlineSuggest.acceptNextLine` にカスタムキーを割り当てることで、この行だけを受け入れることが可能です。


---

## Next Edit Suggestions（NES）

GitHub Copilot の **Next Edit Suggestions（NES）** は、現在の編集に基づいて次に必要な編集箇所を予測し、提案してくれる機能です。
インライン補完が「今この行」を埋めるのに特化しているのに対し、**NES は“変更が波及しそうな次の編集箇所”へ Tab でジャンプできるナビゲーション支援**です。設定で `github.copilot.nextEditSuggestions.enabled` を ON にすると：
- 変数 / 関数 / クラス名を 1 箇所リネーム → 残りの出現箇所を順送りで確認
- 新フィールド追加 → 関連メソッドや計算式への反映漏れを案内
- 条件やロジックの一部変更 → 連動する比較・派生処理の更新候補を提示
“どこをまだ直していないか” の記憶コストを減らし、リネームや構造変更で威力を発揮します。

### 設定の有効化

まず、VS Code の設定で NES を有効化します：

1. **設定** > **拡張機能** > **GitHub Copilot**
2. `github.copilot.nextEditSuggestions.enabled` を **有効** にする

または、設定画面で `Next Edit Suggestions` を検索して有効化してください。

### :pen: 例題3. リネームの自動適用
例題2で作成した Student クラスの `age` プロパティを `yearsOld` にリネームしてみます。
```javascript
// プロパティ: name, yearsOld, grade
```

1. `age` を 1 箇所 `yearsOld` に変更すると、**ガターに矢印**が表示される場合があります
2. **Tabキー** を押すと、次の編集提案（他の `age` 出現箇所）にジャンプ
3. 再度 **Tabキー** で変更を確定し、すべての参照が更新されるか確認

###  :robot: 出力例

```javascript
// リネーム後（例）: age -> yearsOld
class Student {
   constructor(name, yearsOld, grade) {
      this.name = name;
      this.yearsOld = yearsOld;
      this.grade = grade;
   }

   getInfo() {
      return `Name: ${this.name}, YearsOld: ${this.yearsOld}, Grade: ${this.grade}`;
   }

   isAdult() {
      return this.yearsOld >= 18;
   }
}
```

![NES](../images/nes.png)

> 補足: NES の提案が多すぎて視界が騒がしい場合は `editor.inlineSuggest.edits.showCollapsed` を有効にし、必要な時だけ展開する運用が有効です。

---

## :memo: 練習

1. **基本的なコード補完を試す**
   途中まで関数を書いて、ゴーストテキストが表示されたら Tab で受け入れてみる。
   部分受け入れ（Mac: `⌘→` / Windows/Linux: `Ctrl+→`）も試してみてください。

2. **コメント駆動開発を体験**
   コメントで実装したい内容を詳しく書いて、Copilot に生成させてみる。
   - クラスの構造
   - 関数の処理内容
   - アルゴリズムの方針など

3. **複数候補を確かめる**
   Alt + ] / Alt + [ で前後の候補を比較し、好きなものを受け入れてみる。
   `Ctrl + Enter` で複数候補の一括表示も試してみましょう。

4. **Next Edit Suggestions（NES）を活用**
   変数名やクラス名を変更して、ガターの矢印が表示されることを確認。
   Tab キーで次の編集箇所にジャンプし、連鎖的な編集を体験してみましょう。

5. **部分的承諾の活用**
   ログ出力や不要な初期化コードを含む提案が出た場合、一部だけを承諾・残りをスキップしてみましょう。

---

## まとめ

- **基本的なコード補完**: 途中まで書くと Copilot がゴーストテキストで残りを推測・補完
- **コメント駆動開発**: 詳細なコメントを書くことで、意図に沿ったコード構造を生成可能
- **複数の候補**: Alt + ] / Alt + [ で前後を切り替えてベストなものを選択
- **部分的な承諾**: Mac: `⌘→` / Windows/Linux: `Ctrl+→` で単語単位の受け入れが可能
- **Next Edit Suggestions（NES）**: 変数名変更などの編集に基づいて、次の編集箇所を自動予測・提案
