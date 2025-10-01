---

title: ④ ショートカットコマンドの活用
categories: [技術者向け, GitHub Copilot 応用]
weight: 5
---------

GitHub Copilot Chat は、**チャット参加者 (@xxx)**、**スラッシュコマンド (/xxx)**、**チャット変数／ツール（#xxx）**を組み合わ### :bulb: Tips：コンテキストの効果的な使い分け

* **#file:ファイル名** … "特定ファイル参照"

  * **明確にファイルを指定**したい場合に使用。
  * **正確な対象**を指定でき、他のファイルとの混同を防ぐ。

* **#codebase** … "自動検索ツール"

  * プロンプト内容に基づき**コード検索して該当スニペットを文脈注入**。
  * **LLM 側が主導**のまま、**編集/エージェント等の他ツールとも併用可**。
  * **関連ファイルを自動で見つけてくれる**ため、大規模なコードベースで便利。

* **推奨**：特定ファイルが分かっている場合は **#file**、広範囲な関連ファイルを探したい場合は **#codebase** を使用。設計・テスト・デバッグまで回せます。以下は最新仕様に合わせたまとめです。

---

## 1. チャット参加者

先頭に **`@`** を付けて、ドメインごとの“専門家”を呼び出します（拡張や MCP によって参加者が増える場合あり）。

| 参加者            | 役割（要点）                                                                 |
| -------------- | ---------------------------------------------------------------------- |
| **@github**    | GitHub のリポジトリ / Issue / PR などに関する質問。例: `@github What are my open PRs?` |
| **@terminal**  | 統合ターミナルやシェルコマンド関連の質問。例: `@terminal list the 5 largest files`           |
| **@vscode**    | VS Code の機能・設定・拡張 API についての質問。例: `@vscode how to enable word wrap?`    |
| **@workspace** | 現在のワークスペース全般に関する質問。例: `@workspace how is authentication implemented?`  |

---

## 2. スラッシュコマンド

よく使う処理を **`/`** で即呼び出しできます（`/<prompt file name>` でプロンプトファイル実行も可能）。

| コマンド                      | 何をするか（要点）                        |
| ------------------------- | -------------------------------- |
| **/docs**                 | エディタのインラインチャットからドキュメンテーションコメント生成 |
| **/explain**              | コード/ファイル/概念の説明                   |
| **/fix**                  | エラー修正や改善提案                       |
| **/help**                 | チャットの使い方ヘルプ                      |
| **/tests**                | 選択/ファイルの関数・メソッドに対するテスト生成         |
| **/setupTests**           | テストフレームワーク導入の推奨・手順・拡張提案          |
| **/fixTestFailure**       | 失敗しているテストの直し方提案                  |
| **/clear**                | Chat ビューの新規セッション開始               |
| **/new**                  | ワークスペース/ファイルのスキャフォールド            |
| **/newNotebook**          | 指示に基づき Jupyter Notebook を生成      |
| **/search**               | 検索ビュー用クエリを自然言語から生成               |
| **/startDebugging**       | `launch.json` を生成し、デバッグを開始       |

---

## 3. チャットツール

**`#`** で文脈を明示。下表は **VS Code 組み込みツールの全一覧**（MCP や拡張のツールも追加可能）。

| 変数 / ツール                       | 説明                                        |
| ------------------------------ | ----------------------------------------- |
| **#changes**                   | ソース管理の変更一覧                                |
| **#codebase**                  | コード検索で関連箇所を見つけ、スニペットを自動で文脈注入              |
| **#createAndRunTask**          | 新しい **task** を作成して実行                      |
| **#createDirectory**           | ディレクトリ作成                                  |
| **#createFile**                | ファイル作成                                    |
| **#edit** *(tool set)*         | ワークスペースへの編集を有効化                           |
| **#editFiles**                 | ファイルに編集を適用                                |
| **#editNotebook**              | Notebook を編集                              |
| **#extensions**                | VS Code 拡張の検索/質問                          |
| **#fetch**                     | 指定 URL の Web ページ内容を取得して文脈化                |
| **#fileSearch**                | グロブでファイル検索してパスを返す                         |
| **#findTestFiles**             | テストファイルの特定                                |
| **#getNotebookSummary**        | Notebook セル一覧と詳細                          |
| **#getProjectSetupInfo**       | 各種プロジェクトのスキャフォールド手順/設定案内                  |
| **#getTaskOutput**             | 実行した **task** の出力取得                       |
| **#getTerminalOutput**         | ターミナルコマンドの出力取得                            |
| **#githubRepo**                | GitHub リポジトリ内でコード検索                       |
| **#installExtension**          | 拡張機能インストール                                |
| **#listDirectory**             | ディレクトリ内ファイル一覧                             |
| **#new**                       | デバッグ/実行設定込みの新規ワークスペースを生成                  |
| **#newJupyterNotebook**        | 説明に基づく Notebook 生成                        |
| **#newWorkspace**              | 新しいワークスペース作成                              |
| **#openSimpleBrowser**         | Simple Browser を開いてローカル Web アプリをプレビュー     |
| **#problems**                  | Problems パネルの問題を文脈に追加                     |
| **#readFile**                  | ファイル内容を読み取り                               |
| **#readNotebookCellOutput**    | Notebook セルの実行結果取得                        |
| **#runCell**                   | Notebook セルを実行                            |
| **#runCommands** *(tool set)*  | ターミナルでコマンド実行＋出力取得を有効化                     |
| **#runInTerminal**             | 統合ターミナルでシェルコマンド実行                         |
| **#runNotebooks** *(tool set)* | Notebook セル実行を有効化                         |
| **#runTask**                   | 既存の **task** を実行                          |
| **#runTasks** *(tool set)*     | **tasks** 実行＆出力取得を有効化                     |
| **#runTests**                  | ワークスペースのユニットテストを実行                        |
| **#runVscodeCommand**          | 任意の VS Code コマンド実行（例: Zen モード有効化）         |
| **#search** *(tool set)*       | ワークスペースのファイル検索を有効化                        |
| **#searchResults**             | 検索ビューの結果を取得                               |
| **#selection**                 | エディタの選択範囲（テキスト選択時のみ）                      |
| **#terminalLastCommand**       | 直近のターミナルコマンドと出力                           |
| **#terminalSelection**         | ターミナルの選択範囲                                |
| **#testFailure**               | 失敗テスト情報（診断に有用）                            |
| **#textSearch**                | テキスト検索                                    |
| **#todos**                     | TODO の管理（`chat.todoListTool.enabled` が必要） |
| **#usages**                    | 参照/実装/定義の複合（使われ方の調査に便利）                   |
| **#VSCodeAPI**                 | VS Code の機能や拡張開発について質問                    |

> 最新のショートカットコマンド一覧は [ドキュメント](https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features) に記載があるのでご覧ください。

---
## 4. ショートカットコマンドの組み合わせ例

### 4-1. 既存ファイルの改善提案

例題3で作成した `fizzbuzz.js` に対してエラーハンドリングを追加したい場合：

```
/fix #file:fizzbuzz.js
入力値の検証とエラーハンドリングを追加して
```

* **/fix** … エラー解消や改善案を提示
* **#file:** … 対象ファイルを明示し"ズレ"を防止
* 追加の指示で具体的な改善方向を明示

### 4-2. 選択範囲でピンポイント修正

例題3の `fizzbuzz.js` から特定の関数を選択して改善する場合：

1. `fizzBuzz` 関数本体を選択 → 2) インラインチャット（⌘I / Ctrl+I） → 3) 入力:

```
/fix #selection
この関数をより効率的なアルゴリズムに書き直して
```

* **#selection** で過剰な広がりを防ぎ、**/fix** で修正提案

### 4-3. テスト生成と失敗テストの修正

例題3で作成した `fizzbuzz.js` に対してテスト追加：

```
/tests #file:fizzbuzz.js
エッジケース（0、負の数、非数値）も含めて
```

失敗したテストがある場合：

```
/fixTestFailure #testFailure
```

* **/tests** でテスト生成、**/fixTestFailure** で失敗修正案

---

### :bulb: Tips：@workspace と #codebase の違い

* **@workspace** … “参加者”

  * **プロンプトの主導権**を取り、コードベース知識で回答する専用参加者。
  * **他ツールは呼べず**、**Ask モードのみ**で利用。
* **#codebase** … “ツール（検索）”

  * プロンプト内容に基づき**コード検索して該当スニペットを文脈注入**。
  * **LLM 側が主導**のまま、**編集/エージェント等の他ツールとも併用可**。
* **推奨**：柔軟性が高い **#codebase の利用が推奨**。

---

## :memo: 練習

以下の課題で実際にショートカットコマンドを組み合わせて使ってみましょう：

1. **基本的な組み合わせ**
   - 例題3で作成した `fizzbuzz.js` に対して `/explain #file:fizzbuzz.js` でコード解説を依頼
   - `/fix #file:fizzbuzz.js` でコード改善提案を取得

2. **選択範囲を活用した修正**
   - `fizzbuzz.js` の特定の関数を選択して `/fix #selection` で局所的な改善
   - 問題のあるコード部分を選択して `/explain #selection` で問題点を分析

3. **テスト関連の組み合わせ**
   - `/tests #file:fizzbuzz.js` でテストファイル生成
   - テストが失敗した場合は `/fixTestFailure #testFailure` で修正案を取得

4. **実践的な応用**
   - `/search #codebase` で関連コードを検索し、コンテキストを増やして質問
   - `#changes` と組み合わせて変更内容を解説してもらう

> **💡 コツ**: 複数のツールを組み合わせることで、より精度の高い回答や提案を得ることができます。特定ファイルが分かっている場合は `#file`、広範囲な関連ファイルを探したい場合は `#codebase` を使い分けましょう。

---

## まとめ

* **@xxx（参加者）**：GitHub / Terminal / VS Code / Workspace など**領域専門家**を呼び出す
* **/xxx（スラッシュコマンド）**：**説明・修正・テスト・検索・デバッグ開始**まで一発呼び出し
* **#xxx（変数／ツール）**：**ファイル・選択範囲・変更・失敗テスト・検索**等を**文脈に添付**。`#codebase` で自動コード検索が便利

