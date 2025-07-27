---
title: ③ 機能実装
categories: [技術者向け, GitHub Copilot応用]
tags: [実装, リファクタリング, コーディング, 最適化]
weight: 3
---

GitHub Copilotを活用した効率的な機能実装とコード品質向上の実践方法を学びます。新機能の実装からリファクタリング、パフォーマンス最適化まで、Copilotとのペアプログラミングで開発効率を大幅に向上させます。

今回は「Todo削除確認モーダル機能」を例に、実際のコーディングプロセスを実践します。

---

## 1. 事前準備

### :pen: 例題

実装開始前に、設計フェーズで作成したドキュメントをコンテキストとして読み込みます。


### instructionsファイルの準備

実装前に、TypeScript/Reactファイル専用のinstructionsファイルを作成します。

**`.github/instructions/typescript-react.instructions.md`**
```markdown
---
applyTo: "**/*.tsx,**/*.ts"
description: "React TypeScript実装指針"
---

# React TypeScript 実装ガイドライン

## コンポーネント設計
- 関数コンポーネントを使用し、React.FCを明示
- Props インターフェースは同ファイル内で定義
- useState, useCallback, useMemoを適切に活用

## 型定義
- strictモードに準拠した厳密な型定義
- any型の使用禁止
- オプショナルプロパティの適切な使用

## スタイリング
- Tailwind CSSクラスを使用
- 条件付きスタイリングはclsx/classnames活用
- レスポンシブ対応の考慮

## パフォーマンス
- React.memoでコンポーネントをメモ化
- useCallbackでイベントハンドラーをメモ化
- 不要な再レンダリングを防止

## アクセシビリティ
- 適切なARIA属性の付与
- キーボード操作対応
- フォーカス管理の実装
```

> 💡 **補足**: instructionsファイルにより、実装中に毎回同じ指示を与える必要がなくなり、一貫性のある高品質なコードが自動的に生成されます。

> 💡 **awesome-copilotの活用**: 一から作成する代わりに、[awesome-copilot](https://github.com/github/awesome-copilot/)の[ReactJS Development Instructions](https://github.com/github/awesome-copilot/blob/main/instructions/reactjs.instructions.md)をベースにして、プロジェクト固有の要件を追加するとより効率的です。コミュニティで実績のあるベストプラクティスを土台にできます。

---

## 2. 新機能実装

### :pen: 例題

instructionsファイルが準備できたら、設計ドキュメントを参照して実装を開始します。

> 💡 **事前準備**: 実装前に、以下のドキュメントをチャットのコンテキストに追加しておくことで、より一貫性のある実装が可能になります：
> - `要件定義.md` - 機能要件と利用シーン
> - `技術設計.md` - コンポーネント設計と状態管理方針
> - `実装計画.md` - タスク分解と実装順序
>
> ファイルをドラッグ&ドロップするか、`#file:ファイル名`でコンテキストに含めてから実装依頼を行います。

```text
#file:要件定義.md #file:技術設計.md #file:実装計画.md

要件定義、技術設計、実装計画を参考にして、Todo削除確認モーダル機能を実装してください。
```

### :robot: 出力例

```typescript
// components/DeleteConfirmModal.tsx - 新規モーダルコンポーネント
interface DeleteConfirmModalProps {
  isOpen: boolean;
  todoTitle: string;
  onConfirm: () => void;
  onCancel: () => void;
}

export const DeleteConfirmModal: React.FC<DeleteConfirmModalProps> = ({
  isOpen,
  todoTitle,
  onConfirm,
  onCancel,
}) => {
  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div className="bg-white rounded-lg p-6 max-w-sm w-full mx-4">
        <h3 className="text-lg font-semibold mb-2">削除確認</h3>
        <p className="text-gray-600 mb-4">
          「{todoTitle}」を削除しますか？
        </p>
        <div className="flex gap-3 justify-end">
          <button
            onClick={onCancel}
            className="px-4 py-2 text-gray-600 border border-gray-300 rounded hover:bg-gray-50"
          >
            キャンセル
          </button>
          <button
            onClick={onConfirm}
            className="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600"
          >
            削除する
          </button>
        </div>
      </div>
    </div>
  );
};

// components/TodoItem.tsx - 修正版
import { useState } from 'react';
import { DeleteConfirmModal } from './DeleteConfirmModal';

export const TodoItem: React.FC<TodoItemProps> = ({ todo, onToggle, onDelete }) => {
  const [showDeleteModal, setShowDeleteModal] = useState(false);

  const handleDeleteClick = () => {
    setShowDeleteModal(true);
  };

  const handleDeleteConfirm = () => {
    onDelete(todo.id);
    setShowDeleteModal(false);
  };

  return (
    <>
      <div className="flex items-center gap-3 p-3 border rounded">
        <input
          type="checkbox"
          checked={todo.completed}
          onChange={() => onToggle(todo.id)}
        />
        <span className={todo.completed ? 'line-through text-gray-500' : ''}>
          {todo.title}
        </span>
        <button
          onClick={handleDeleteClick}
          className="ml-auto text-red-500 hover:text-red-700"
        >
          削除
        </button>
      </div>

      <DeleteConfirmModal
        isOpen={showDeleteModal}
        todoTitle={todo.title}
        onConfirm={handleDeleteConfirm}
        onCancel={() => setShowDeleteModal(false)}
      />
    </>
  );
};
```

> 💡 **補足**: より本格的な実装では、ESCキーでのモーダルクローズ、フォーカス管理、ARIA属性による アクセシビリティ対応、アニメーション効果、ポータル（ReactDOM.createPortal）の活用などの機能を含めると実用性が向上します。

---

## 3. エラーが起きた際の対処法
### エディタ上で確認できる問題
#### 問題が起きている箇所を選択して「クイックフィックス」を実行する
エディタ上で赤波線が引かれており警告が出ている部分に関しては、該当箇所をホバーしてクイックフィックスをすることで解消されることがあります。
ただし、ファイルをまたがる修正が必要なパターンだと適切に修正されないケースがあるので注意です。

1. 問題が起きている箇所にマウスを合わせる
2. 問題の内容が表示され、クイックフィックスボタンが表示されるのでクリック
![クイックフィックス1](../images/quickfix1.png)
3. 「Copilot を使用して修正する」を選択 (他のクイック修正で解決できそうであれば選択してもOK)
![クイックフィックス2](../images/quickfix2.png)


#### 問題タブよりコンテキストに含める
問題が起きており警告が出ている箇所の一覧が問題タブに表示されています。
該当の問題をチャットのコンテキストに追加し、修正依頼を出すことでファイルをまたがる修正が必要でも対処できます。

1. 問題タブを開く
2. 該当の問題をドラッグしてチャット欄でドロップする
![問題タブのドラッグ&ドロップ](../images/problem_tab_drag.png)
3. 問題の修正依頼を出す

### ターミナル上で確認できるエラー
ターミナル上でアプリケーションのエラーログやビルドに関するエラーが出ている際に、ターミナルの情報をコンテキストとして修正依頼を出すことも可能です。
(例)pnpm build で発生したビルドエラー

1. ターミナルのエラー内容を選択
2. 下記のようなプロンプトで修正を指示

```text
#terminalSelection 発生しているビルドエラーを解消して
```
![ターミナルの選択](../images/terminal_selection.png)

---

## :memo: 練習

1. **基本的な機能実装**
   - Todo編集機能のモーダル実装
   - カテゴリ選択機能の追加
   - 優先度設定機能の実装

2. **エラーハンドリングの実践**
   - 意図的にエラーを発生させて修正依頼
   - 問題タブからのコンテキスト追加を体験
   - ターミナルエラーの修正を実践

3. **コード品質向上**
   - 既存コードのリファクタリング
   - パフォーマンス最適化の実装
   - アクセシビリティ対応の追加

4. **instructionsファイルの最適化**
   - awesome-copilotのinstructionsをベースにカスタマイズ
   - プロジェクト固有のルールを追加
   - チーム向けのコーディング規約を策定

> 実装フェーズでは、Copilotを「ペアプログラミングのパートナー」として活用し、コードの品質と開発効率の両方を向上させることが重要です。エラーが発生した際も、適切なコンテキストを提供することで迅速な問題解決が可能になります。

---

## まとめ

* **instructionsファイル**でプロジェクト固有のコーディング規約を自動適用
* **awesome-copilot**のコミュニティリソースを活用した効率的な開発環境構築
* **設計ドキュメント**をコンテキストに含めることで一貫性のある実装を実現
* **エラー対処法**の習得により、問題発生時の迅速な解決が可能
* **VS Codeの機能**（問題タブ、ターミナル選択）を活用したCopilotとの効果的な連携


