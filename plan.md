いいね、その流れで「基本的な使い方」を1問1答でテンポよく回せるハンズオン案、作りました。各問は**Q（お題）→やること→チェック**の超短サイクルで、参加者がすぐ手を動かせるようにしてあります。参照元ドキュメントも横に置けるように付けてます。

---

# 基本のハンズオン（1問1答・即試し）

## 1) コード補完（インラインサジェスト & NES）

**Q1.** 「関数の次の1行を Copilot に書かせよう」
やること：任意の言語で未完成の関数を書き始める → ゴーストテキストが出たら **Tab** で受け入れ。
チェック：提案を**部分受け入れ**（Mac: ⌘→ / Win/Linux: Ctrl+→）も試せた？ ([Visual Studio Code][1])

**Q2.** 「コメント駆動でクラスを生やす」
やること：`// TypeScript で Student クラスを…` のようなコメントを書く → 生成提案を受け入れ。
チェック：コメントの意図に沿った構造になった？ ([Visual Studio Code][1])

**Q3.** 「Next Edit Suggestions（NES）を体感」
やること：設定で `github.copilot.nextEditSuggestions.enabled` を有効化 → 変数名を1か所変更 → 次の編集提案に **Tab** でジャンプ&確定。
チェック：ガターの矢印メニューが見え、連鎖編集が提案された？ ([Visual Studio Code][1])

## 2) インラインチャット（エディタ & ターミナル）

**Q4.** 「選択コードだけをリファクタ」
やること：コードを選択 → **⌘I**（Win/Linux: **Ctrl+I**）で**インラインチャット** → `非同期/awaitで書き換えて` と依頼 → 受け入れ/差し戻し。
チェック：選択範囲だけが安全に置換された？ ([Visual Studio Code][2])

**Q5.** 「ターミナルの失敗コマンドを直す」
やること：統合ターミナルを開く（Mac: ⌃`）→ **⌘I**（Win/Linux: Ctrl+I）→ `直近のエラーの原因は？修正コマンド出して`
チェック：**⌘Enter**（Win/Linux: Ctrl+Enter）で直接実行できた？ ([Visual Studio Code][2])

## 3) Copilot Chat：Ask / Edit / Agent の違いを掴む

**Q6.** 「Ask で“調べて・教えて・部分適用”」
やること：**Chat ビュー**（Mac: ⌃⌘I / Win/Linux: Ctrl+Alt+I）→ モード「Ask」→ `この関数の計算量は？` と聞く → 返答の**コードブロックを Apply in Editor**。
チェック：適用ボタンから該当ファイルへスマートに反映された？ ([Visual Studio Code][3])

**Q7.** 「Edit で“複数ファイルを一括編集”」
やること：モード「Edit」→ **Add Context**で対象ファイルを追加 or `#codebase` で横断 → `Auth を OAuth に差し替え` など**編集系**の依頼。
チェック：エディタ上に**ストリーミングで差分**が現れ、確認しながら進められた？ ([Visual Studio Code][4])

**Q8.** 「Agent で“計画→ツール→反復”を体験」
やること：モード「Agent」→ `React+Node で ToDo アプリを作って` → **Tools**を確認（必要なら有効化）→ 実行の都度**承認**。
チェック：エージェントがファイル選定・ツール実行（テスト/ターミナル等）を**自律的に反復**できた？ ([Visual Studio Code][5])

## 4) ショートカット & コマンドを手に馴染ませる

**Q9.** 「3つだけ覚える」
やること：

* **インラインチャット**：⌘I / Ctrl+I
* **Chat ビュー**：⌃⌘I / Ctrl+Alt+I
* **Agent モード切替**：⇧⌘I / Ctrl+Shift+I（チートシート参照）
  チェック：3キーで Ask/Edit/Agent を行き来できる？

**Q10.** 「スラッシュコマンドと @ 参加者」
やること：Ask で `/explain` `/fix` を入力、`@terminal` でシェル系質問を投げる。
チェック：候補ポップアップが使え、回答精度が上がった？ ([Visual Studio Code][3])

## 5) コンテキスト追加（# と Add Context）

**Q11.** 「#で“今見るべき範囲”を縛る」
やること：Ask/Edit で `#ファイル名` `#フォルダ` `#symbol` を付与、`#changes` や `#codebase` も試す。
チェック：回答が**該当箇所に限定**され、無関係な説明が減った？ ([Visual Studio Code][6])

**Q12.** 「ドラッグ&ドロップで文脈投入」
やること：エクスプローラーやタブから Chat ビューへ**ファイル/フォルダを D&D**。
チェック：添付した構造（全文/アウトライン）がプロンプトに反映された？ ([Visual Studio Code][6])

**Q13.** 「Web/GitHubの外部知識も混ぜる」
やること：`#fetch <URL>`、`#githubRepo org/repo` を Ask で使用。
チェック：外部資料を**引用した回答**になった？ ([Visual Studio Code][6])

## 6) プロンプトエンジニアリング（最小の型）

**Q14.** 「“目的→制約→出力形式→評価基準”の4点セット」
やること：`目的: ○○を実装、制約: TypeScript/React18、出力: 差分パッチ、基準: eslint ルール遵守` のように短く構造化。
チェック：冗長な説明が減り、**そのまま適用できる差分**が返ってきた？ ([Visual Studio Code][7])

**Q15.** 「大きい依頼は刻む」
やること：「アプリ作成→ページ追加→API 実装→テスト生成」のように**分割して反復**。
チェック：各ステップの精度が上がりリトライが減った？ ([Visual Studio Code][7])

## 7) PR/コミット文の自動生成（Smart Actions）

**Q16.** 「差分からコミット文/PR下書きを自動生成」
やること：**ソース管理**ビューのスパークル（✨）から「Generate Commit Message」→ GitHub Pull Requests 拡張があれば PR タイトル/説明も生成。
チェック：要約の網羅性と誤りを目視で確認→必要なら手直し。 ([Visual Studio Code][8])

## 8) コードレビュー（VS Code と GitHub 上）

**Q17.** 「エディタでローカル差分をレビュー」
やること：対象コードを選択→ 右クリック **Generate Code > Review** でレビューコメント生成（PR全体は PR 拡張の Files Changed から）。
チェック：**Comments パネル/インライン**に指摘が作られた？ ([Visual Studio Code][8])

**Q18.** 「GitHub で Copilot をレビュアーに招待」
やること：GitHub の Pull Request で **Reviewers** に **Copilot** を追加 → コメントを読む/提案を適用。
チェック：Copilot のレビューは**常に “Comment” レビュー**で、必須承認カウントに含まれない点を理解できた？（再レビューは Reviewer の Copilot 横のボタン） ([GitHub Docs][9])

---

## 進め方の提案（所要 45–60 分目安）

1. **ウォームアップ（Q1–Q3）**：補完→部分受け入れ→NES
2. **対話の3モード（Q4–Q8）**：インライン→Ask→Edit→Agent
3. **操作を馴染ませる（Q9–Q10）**：3ショートカット＋/・@
4. **文脈とプロンプト（Q11–Q15）**：#やD&D、分割リクエスト
5. **レビューとPR（Q16–Q18）**：Smart Actions → GitHub レビュー

---

## 参考（公式ドキュメント）

* コード補完・NES：Code Completions（Tab 受け入れ、部分受け入れ、NES 有効化） ([Visual Studio Code][1])
* インラインチャット（エディタ/ターミナル、⌘I / Ctrl+I） ([Visual Studio Code][2])
* Ask モード（Chat ビュー、⌃⌘I / Ctrl+Alt+I、適用ボタン、Quick Chat） ([Visual Studio Code][3])
* Edit モード（複数ファイル編集、#codebase） ([Visual Studio Code][4])
* Agent モード（ツール使用、承認、反復） ([Visual Studio Code][5])
* チートシート（主要ショートカット）
* コンテキスト追加（#、Add Context、D&D、@participants、#fetch/#githubRepo） ([Visual Studio Code][6])
* プロンプト作法（タスク分割・具体化・# 参照） ([Visual Studio Code][7])
* Smart Actions（コミット/PR 生成、レビュー実行） ([Visual Studio Code][8])
* GitHub でのコードレビュー（Reviewerに Copilot、“Comment” レビュー、再レビュー） ([GitHub Docs][9])

---

必要なら、このまま**スライド化**や**配布用チートシート（A4 1枚）**にも落とし込みます。どの言語でデモする予定か教えてくれれば、各Qの**サンプルコード**も即用意するよ。

[1]: https://code.visualstudio.com/docs/copilot/ai-powered-suggestions "Code completions with GitHub Copilot in VS Code"
[2]: https://code.visualstudio.com/docs/copilot/chat/inline-chat "Inline chat"
[3]: https://code.visualstudio.com/docs/copilot/chat/chat-ask-mode "Use ask mode in VS Code"
[4]: https://code.visualstudio.com/docs/copilot/chat/copilot-edits "Use edit mode in VS Code"
[5]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode "Use agent mode in VS Code"
[6]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat-context "Manage context for AI"
[7]: https://code.visualstudio.com/docs/copilot/chat/prompt-crafting "Prompt engineering for Copilot Chat"
[8]: https://code.visualstudio.com/docs/copilot/copilot-smart-actions "AI smart actions in Visual Studio Code"
[9]: https://docs.github.com/ja/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review "GitHub Copilot コード レビューの使用 - GitHub Docs"
