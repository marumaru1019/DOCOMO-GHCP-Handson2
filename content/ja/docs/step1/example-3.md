---
title: ③ チャット
categories: [技術者向け, GitHub Copilot 基本]
weight: 3
---

**Copilot Chat** は VS Code 上で会話しながら、設計相談・コード提案・差分適用・自動実行（ツール連携）まで行える機能です。本コンテンツでは“チャットの使い方”として、モデル選択・モード選択・送信、コンテキスト追加、過去リクエストの編集を学びます。

---

## モード早見表
Copilot Chat には 3 つのモードが有り下記のような特徴があります。
現在だと、基本的に Agent モードを使用するのが主流となっております。

| 項目          | **Ask モード**                   | **Edit モード**                                             | **Agent モード**                                           |
| ----------- | ----------------------------- | -------------------------------------------------------- | ------------------------------------------------------- |
| 主目的         | コード/技術に関する質問・説明・方針検討（Q&A）     | 指定したファイル群へのコード編集提案をその場で反映                                | 高レベル要件から**自律的に計画→編集→（必要に応じて）ツール/ターミナル実行→反復**            |
| 代表的な使いどころ   | 「この関数の役割は？」「実装案を3つ」などの理解/設計相談 | 「このファイル群を async/await へ」「このクラスにテストを追加」など**範囲が分かっている変更**  | 「OAuth でログイン導入」「Redis キャッシュ化」など**複数ファイル＋コマンド実行を伴う複雑作業** |
| 典型プロンプト例    | 「DB接続はどこ？」「このソート関数を解説して」      | 「`services/*.ts` をDIに統一」「`Calculator` に Vitest で単体テスト追加」 | 「既存認証をOAuthへ置換」「React→Vueへ移行計画を立て実装」                    |


---

##  :pen: 例題1. チャットの使い方

**目的**：Chat ビューでモードを切り替え、モデルを選び、プロンプトを送信する基本操作を掴む。

**手順**

1. **Chat ビューを開く**

   * ショートカット：**Mac** `⌃⌘I` / **Windows・Linux** `Ctrl+Alt+I`。
   * または検索窓の右側のチャットアイコンをクリックする。
   ![チャットアイコン](../images/chaticon.png)
2. **モードを選択**

   * 入力欄上の**モードドロップダウン**から *Ask / Edit / Agent* を選択(デフォルトは Agent)。
3. **モデルを選択**

   * 入力欄の**モデルピッカー**で使用する言語モデルを選ぶ（契約や設定により候補は変動）。
4. **プロンプトを入力して送信**

   * 入力欄に質問を書き、**Enter** または送信アイコンで送る。

   ![チャット](../images/chatview.png)

**プロンプト例**

```text
JavaScript で FizzBuzz を最小限に実装して fizzbuzz.js に出力して。
可読性重視、1～20を例で出力して。
```

##  :robot: 出力例

```javascript
/**
 * FizzBuzz実装
 * 可読性を重視したシンプルな実装
 */

/**
 * FizzBuzzの判定と出力を行う関数
 * @param {number} num - 判定する数値
 * @returns {string} - FizzBuzz、Fizz、Buzz、または数値の文字列
 */
function fizzBuzz(num) {
    if (num % 15 === 0) return "FizzBuzz";
    if (num % 3 === 0) return "Fizz";
    if (num % 5 === 0) return "Buzz";
    return num.toString();
}

/**
 * 指定した範囲でFizzBuzzを実行
 * @param {number} start - 開始数値
 * @param {number} end - 終了数値
 */
function runFizzBuzz(start, end) {
    console.log(`FizzBuzz: ${start}～${end}`);
    console.log("==============================");

    for (let i = start; i <= end; i++) {
        console.log(`${i}: ${fizzBuzz(i)}`);
    }
}

// 1～20の例を実行
runFizzBuzz(1, 20);

// Node.js モジュールとして利用できるようにエクスポート
if (typeof module !== 'undefined' && module.exports) {
    module.exports = { fizzBuzz, runFizzBuzz };
}
```

> **💡 Tips: 目的別のモデル選択**
> | 目的/場面                          | 推奨モデル（代表例）                                                                      | 使いどころの目安                                                              |
| ------------------------------ | ------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| **汎用のコーディング/文章生成**             | GPT-4.1 / GPT-5-Codex / Claude Sonnet 3.7 / Gemini 2.0 Flash / Grok Code Fast 1 | 普段の関数実装・説明・ドキュメント化などの“まずこれ”枠。速度と品質のバランスが良い。        |
| **軽量タスク（速さ重視）**                | o4-mini / Claude Sonnet 3.5 / Gemini 2.0 Flash                                  | 小さな関数の追加、構文質問、サッと試すプロトタイピング。低レイテンシ寄り。               |
| **深い推論・大規模リファクタ・難バグ調査**        | GPT-5 mini / GPT-5 / o3 / Claude Sonnet 4 / Gemini 2.5 Pro    | 複数ファイルに跨る変更、設計判断、段階的なデバッグなど“考える系”。                |
| **画面/図を見せて質問（Vision）**         | GPT-4.1 / Gemini 2.0 Flash / Gemini 2.5 Pro / Claude Sonnet 4 / Claude Opus 4   | UIスクショ・ダイアグラムの理解やフロントの挙動説明に。※視覚入力を受け取れる文脈で使用。      |
| **Agent モード（自律的に計画→編集→ツール実行）** | 上の**深い推論**系モデルが無難（ツール呼び出し対応モデルのみ）                                               | 複雑作業を一気に進めたい時。Agent ではツールコーリング対応モデルだけが選べます。  |

> 補足：利用できるモデルはプラン/クライアントごとに異なるうえ、提供は入れ替わります（たとえば一部は「Closing down」予定の記載あり）。実環境で選べるモデルは[公式の対応表](https://docs.github.com/en/copilot/reference/ai-models/model-comparison)を確認してください。

---

## チャットへのコンテキストの追加

Copilot Chat により関連性の高い回答を得るには、適切なコンテキストを提供することが重要です。VS Code では、**暗黙的なコンテキスト**と**明示的なコンテキスト**の2種類のコンテキスト追加方法があります。

---

### 暗黙的なコンテキスト

VS Code は現在のアクティビティに基づいて、自動的にコンテキストをチャットプロンプトに追加します：

- **現在選択中のテキスト**：エディタで選択されているコード
- **アクティブなファイル名**：現在開いているファイルまたはノートブック
- **Ask モードまたは Edit モード**：アクティブなファイルが自動的にコンテキストとして含まれる

> **補足**: Agent モードでは、エージェントツールが自動的にアクティブファイルをコンテキストに追加すべきか判断します。

---

### 明示的なコンテキスト追加方法

チャットに明示的にコンテキストを追加する方法は複数あります：

#### 1. **#-メンション** でコンテキストを参照

入力欄で `#` を入力すると、利用可能なコンテキスト項目のリストが表示されます。

**主なコンテキスト項目:**
- `#file <path>`: 単一ファイルを明示的に参照
- `#codebase`: リポジトリ横断検索を有効化（ワークスペース全体から関連ファイルを自動検索）
- `#changes`: ソース管理の変更差分を参照
- `#selection`: 現在のエディタ選択範囲を参照
- `#terminalSelection`: ターミナルの選択範囲を参照
- `#problems`: Problems パネルのワークスペースの問題を参照

#### 2. **Add Context** ボタンを使用

![コンテキスト](../images/addcontext.png)

Chat ビューの入力欄にある **Add Context** ボタンをクリックしてコンテキストを追加できます：

**主なコンテキスト項目:**

- **Files & Folders**: ワークスペース内のファイルやフォルダ
- **Symbols**: クラス、関数、メソッドなどのコードシンボル
- **Tools**: `#codebase`、`#fetch` などの組み込みツール

#### 3. **ドラッグ＆ドロップ**

以下の要素をチャットビューにドラッグ＆ドロップできます：

- Explorer ビューや検索ビューからファイルやフォルダ
- Problems パネルの項目

#### 4. **外部ソースの参照**

- `#fetch <URL>`: 特定の Web ページのコンテンツを取得（APIリファレンスやドキュメントの参照に便利）
- `#githubRepo <owner/repo>`: GitHub リポジトリ内でコード検索を実行

> **:bulb: Tips**:
> - `#codebase` を使用すると、どのファイルが関連しているか不明な場合でも、VS Code が自動的に適切なファイルを見つけます
> - ファイルを参照する際は、可能な限り完全な内容が含まれます。大きすぎる場合は、関数の概要（実装なし）が含まれます
> - 設定 `github.copilot.chat.codesearch.enabled` を有効にすると、より良い検索結果が得られます

---

###  :pen: 例題2. ファイル参照でテストコード生成

**目的**：例題1で作成した `fizzbuzz.js` のロジックを理解させ、テストコードを自動生成させる。

**手順**
1. 例題1の FizzBuzz 実装を `fizzbuzz.js` として保存。
2. Chat ビューで **Add Context** からそのファイルを追加、または入力欄で `#file fizzbuzz.js`。
3. 併せて `#codebase` を付与すると関連使用例も探索可能。
4. プロンプト送信。

**プロンプト例**
```text
#file fizzbuzz.js を読み Jest の単体テストを生成。ケース: 1..20 を網羅し、15/3/5/その他をグループ化。失敗例も1件含めて。
```

### :robot: 出力例
```javascript
const { fizzBuzz } = require('../fizzbuzz');

describe('FizzBuzz - 1..20 の網羅', () => {
  test('3の倍数はFizz、5の倍数はBuzz、15の倍数はFizzBuzzになる', () => {
    const expected = {
      3: 'Fizz', 5: 'Buzz', 6: 'Fizz', 9: 'Fizz', 10: 'Buzz', 12: 'Fizz', 15: 'FizzBuzz', 18: 'Fizz', 20: 'Buzz'
    };

    for (const [numStr, expectedVal] of Object.entries(expected)) {
      const num = Number(numStr);
      expect(fizzBuzz(num)).toBe(expectedVal);
    }
  });

  test('1..20 を順にチェック（グループ化: 15/3/5/その他）', () => {
    const groups = {
      fizzbuzz: [15],
      fizz: [3,6,9,12,18],
      buzz: [5,10,20],
      others: [1,2,4,7,8,11,13,14,16,17,19]
    };

    for (const n of groups.fizzbuzz) expect(fizzBuzz(n)).toBe('FizzBuzz');
    for (const n of groups.fizz) expect(fizzBuzz(n)).toBe('Fizz');
    for (const n of groups.buzz) expect(fizzBuzz(n)).toBe('Buzz');
    for (const n of groups.others) expect(fizzBuzz(n)).toBe(n.toString());
  });

  test('意図的失敗ケース（ナンバー7をFizzにする誤期待）', () => {
    // ここでは意図的に期待値を間違えて1件失敗させる
    expect(fizzBuzz(7)).toBe('Fizz');
  });
});

```

---

### :pen: 例題3. Web ページ参照でモデル列挙

**目的**：`#fetch` で Azure AI Foundry (OpenAI) の概要ページを取得し、利用可能モデルを分類・要約させる。

**手順**
1. `#fetch https://learn.microsoft.com/ja-jp/azure/ai-foundry/openai/overview` を送信。
2. 続けて整理したい粒度（性能指標・料金比較観点など）を追加プロンプトで指定。

**プロンプト例**
```text
#fetch https://learn.microsoft.com/ja-jp/azure/ai-foundry/openai/overview を読み、
Azure OpenAI で使用できるモデルを教えて。
```

### :robot: 出力例
```
指定ページを読み、Azure OpenAI（Azure AI Foundry 内）で利用可能と明記されているモデル群を抽出しました。以下はページに記載されている主なモデルの一覧です（ページ記載の表現や関連リンクに基づくまとめ）。

Gpt-5 シリーズ
o4-mini
o3
gpt-4.1
o3-mini
o1
o1-mini
GPT-4o
GPT-4o mini
GPT-4 Turbo with Vision
GPT-4（GPT-4 系全般）
GPT-3.5-Turbo シリーズ
埋め込み（Embeddings）シリーズ
GPT-image-1（画像生成・編集モデル） — プレビュー記載
DALL-E（言及あり）
音声（音声→テキスト / テキスト→音声）関連モデル（Realtime Audio、音声モデル群）
テキスト読み上げモデル（プレビュー）
Codex（コード生成関連のモデル、言及あり）
```

---

## :pen: 例題4. 過去のリクエスト内容の変更（編集して再送 / チェックポイント）

**目的**：直前の指示ミスや条件追加を“過去メッセージを編集”してやり直す。必要ならワークスペースも**チェックポイント**で巻き戻す。

**手順**

1. **編集したいリクエストを選択**

   * チャット内で該当メッセージに**ホバー**し、クリックする。
2. **内容を修正して再送**

   * メッセージ内容を編集する(メッセージの編集にはモデルやモードの変更も含まれます)。送信すると、そのメッセージ以降の履歴および変更内容は取り消され、当該メッセージの内容で**新しいレスポンス**が返る。
   ![リクエストの編集](../images/request-edit.png)
3. **必要ならチェックポイントを復元**

   * 設定 `chat.checkpoints.enabled` を有効化すると、要所でスナップショットが作られる。復元は対象メッセージにホバー→**Restore Checkpoint**→確認。**Redo**でやり直しも可能。
   ![チェックポイント](../images/checkpoint.png)

> **注意**: 複数個前のメッセージを編集した場合、そこまでに行ったチャット履歴は削除され変更は巻き戻ります。この巻き戻りに関してはやり直しが聞かないので気をつけてください。

---

## :memo: 練習

**課題**：

1. Chat ビューを開き、**Ask** を選択し、**モデルピッカー**で任意のモデル（または **Auto**）を選ぶ。
2. `#file csv_utils.js` と `#codebase` を使って「例外安全化とテスト観点の抜け」を要約させ、方針を決める。
3. **過去のリクエストを編集**して、方針に「JSDoc コメント必須」「丸め規則は統一」を追加して再送。
4. 変更が広範囲なら、状況に応じて**チェックポイントの復元/Redo**も試す。
   （JavaScript での実装例・テスト雛形を返せたら合格）

---

## まとめ

* **まずは Ask で理解と方針を固める → Edit で差分適用 → 必要に応じて Agent で実行を任せる。**
* **コンテキスト**（`#file` / `#codebase` / Add Context / 画像 / ツール / 参加者）を適切に渡すほど精度は上がる。
* **過去リクエストの編集**と**チェックポイント**で、やり直しや巻き戻しが安全にできる。

---
