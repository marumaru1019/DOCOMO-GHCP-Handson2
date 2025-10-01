---

title: ⑤ コミット/PR 文の自動生成
categories: [技術者向け, GitHub Copilot 基本]
weight: 7
---

**差分からコミット文**や**PR のタイトル・説明**を VS Code 上で自動生成できます。
Source Control（ソース管理）や PR 作成画面にある ✨（スパークル）から呼び出せるため、チャットに切り替えずに下書き→人が最終チェック→提出までを素早く回せます。

---

## :pen: 例題1. 差分からコミット文を自動生成

**前提**

* Git 管理下のワークスペース
* 変更の一部を **Stage** 済み（コミット対象が明確になります）

**手順**

1. アクティビティバーの **ソース管理** を開く。
2. コミットメッセージ入力欄右側の **✨（コミットメッセージの生成）** をクリック。
3. 提案が挿入されるので、**網羅性・正確性**を目視でチェックして、必要なら手直ししてからコミット。
   ![Generated-Commit-Message](../images/generated-commit-message.png)

---

## :pen: 例題2. PR タイトル／説明を自動生成

**前提**

* [**GitHub Pull Requests**](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github) 拡張をインストール (なければチャットで `#installExtension
GitHub Pull Requests` と送信しましょう)
![拡張機能](../images/github-pull-requests-extension.png)
* ブランチを push 済みで **Create Pull Request** 画面にいる

**手順**

1. PR 作成画面で **Title** 欄右の **✨（Generate）** をクリック。
2. 生成された **タイトル** と **説明** を確認し、必要な補足（背景・目的・テスト方法・破壊的変更の有無など）を追記。
3. 作成前に差分タブやチェックを確認してから **Create**。
   ![Generated-PR-Description](../images/generated-pr-description.png)


---

## :memo: 練習

**課題**：

1. 例題3で作成した `fizzbuzz.js` にエラーハンドリング機能を追加（入力値の検証など）。
2. `fizzbuzz.test.js` を追加して Jest テストを実装。
3. 変更を **Stage** → Source Control の **✨** で **コミット文**を生成して手直し → コミット。
4. ブランチを push → PR 作成画面で **✨** を使い **タイトル／説明**を生成し、**テスト方法の記述**を追記して作成。

---

## まとめ

* **コミット文**と**PR 情報**は ✨ からワンクリック生成 → **人が最終チェック**して整える。
* まずは反復作業を自動化し、レビューの本質（品質・安全性）に時間を使う。
