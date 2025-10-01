---

title: ⑥ コードレビュー
categories: [技術者向け, GitHub Copilot 基本]
weight: 8
---

GitHub Copilot のコードレビューは、PR や IDE 上の変更に対して AI がレビューコメントを生成し、場合により**Suggested changes**（そのまま適用できる提案）も返してくれる機能です。レビューは **Web Browser（GitHub.com）** と **IDE（VS Code）** の両方から行えます。

---

## :pen: 例題1.  Web Browser 上でのレビュー依頼

**目的**：既存のPRに対して Copilot にレビューを依頼し、コメントを確認・反映する。

**手順**

1. GitHub.comで対象リポジトリの **Pull requests** からPRを開く。
2. **Reviewers** セクションで **Copilot** を選択して **Request** をクリック。

   ![Request-Review](../images/request-review.png)
3. 生成されたレビューコメントを確認。**Suggested changes** が出ている場合はその場で適用し、**Commit** する。
   ![Review-Comments](../images/review-comments.png)

---

## :pen: 例題2. IDE 上のレビュー（選択コードのクイックレビュー）

**目的**：ファイル内の**選択範囲だけ**を素早くレビューして指摘を得る（軽量な一次チェック）。

**手順**

1. VS Codeでレビューしたい数十行を**選択**。
2. コマンドパレットより **GitHub Copilot: レビュー**を選択。
   ![Context-Menu-Review](../images/context-menu-review.png)
3. 下部パネル（またはエディタ内）に**レビュー結果**が表示される。指摘に従って修正。
   ![Inline-Review-Result](../images/inline-review-result.png)


---

## :pen: 例題3. IDE 上のレビュー（未コミット変更の**全体レビュー**）

**目的**：ワークスペースの**未コミット差分全体**をまとめてレビューし、横断的な指摘や修正案を得る（深いレビュー）。

**手順**

1. VS Codeの **Source Control（ソース管理）** ビューを開く。
2. ビュー上部の **Code Review（コミットされていない変更）** ボタンを押す。

   ![SC-Code-Review-Button](../images/sc-code-review-button.png)
3. 解析が完了すると、**コメント一覧**や**ファイルごとの指摘**が表示されるので、必要に応じて**提案を適用**する。
   ![SC-Comments-Panel](../images/sc-comments-panel.png)


---
## :memo: 練習

**課題**：例題で作成したJavaScriptファイルで小さな修正を2ファイルに入れたうえで、

1. **IDE**で「選択レビュー」を実行 → 指摘のうち1つを手動修正。
2. 続けて「未コミット全体レビュー」を実行 → **Suggested changes**を1つ適用してコミット。
3. ブランチをpush → **Web**でPRを開き、**Copilot** をReviewerにして **Request** → 追加で1件以上の指摘を反映。
   （差分・コメント・コミット履歴にレビュー結果が反映されていれば合格）

---

## まとめ

* **Web（GitHub.com）**：既存PRに対してCopilotをReviewerとして追加し、本格的なレビューとSuggested changes適用
* **IDE（VS Code）**：選択範囲の軽量レビューから未コミット変更の全体レビューまで、開発中の品質チェック
* **使い分け**：手元での事前チェックはIDE、PRでのフォーマルレビューはWebが効果的
* **自動化**：リポジトリ設定で新規PR作成時の自動レビューも可能

> コードレビューをAIが支援することで、ヒューマンレビューはより高次な設計や仕様の観点に集中でき、開発チーム全体の品質向上と効率化を実現できます。
