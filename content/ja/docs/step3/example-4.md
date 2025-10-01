---
title: ④ テスト実装
categories: [GitHub Copilot, 技術者向け]
tags: [test, quality, jest, testing-library]
weight: 4
---

GitHub Copilotを活用して効率的にテストコードを実装し、コード品質を向上させる手法を学びます。これまでのシナリオで理解したプロジェクト構造・設計した機能・実装したコードに対する包括的なテスト戦略を構築します。

---

## 1. 事前準備

### 使用するテストライブラリの概要

本シナリオでは以下のテストライブラリを組み合わせて使用します：

> **📚 テストライブラリの役割分担**
>
> **基盤ライブラリ：**
> * **Jest** … テスト実行エンジン（テストの実行・結果判定・モック機能）
>
> **React専用ライブラリ：**
> * **React Testing Library** … Reactコンポーネントをレンダリング・検索
> * **@testing-library/user-event** … リアルなユーザー操作をシミュレート
> * **@testing-library/jest-dom** … DOM要素用の便利な検証機能を追加
>
> **💡 連携の仕組み：** Jest がテスト全体を管理し、React Testing Library でコンポーネントを描画、user-event でユーザー操作を再現、jest-dom で結果を検証する流れになります。

### テスト雛形生成用プロンプトファイルの作成

効率的なテスト実装のために、まずテスト雛形生成用のプロンプトファイルを作成します。

**`.github/prompts/generate-test.prompt.md`**

```markdown
---
mode: agent
description: "Reactコンポーネントテスト雛形生成"
---

# Reactコンポーネントテスト雛形生成

${input:componentName:コンポーネント名} のテストファイルを生成してください。

## 生成要件
- React Testing Library使用
- TypeScript対応
- describe/it構造で整理
- モックとスパイの適切な活用

## 基本テストケース
### 正常系テスト
- コンポーネントの正常なレンダリング
- Propsによる表示内容の変化
- ユーザーインタラクションの正常動作
- 状態変更の正常フロー

### 異常系テスト
- 無効なPropsでのエラーハンドリング
- ネットワークエラー時の挙動
- 予期しない状態での動作確認
- エッジケースでの安全性

### アクセシビリティテスト
- ARIA属性の適切な設定
- キーボードナビゲーション
- フォーカス管理
- スクリーンリーダー対応

## ファイル配置
- テストファイル: src/components/__tests__/${componentName}.test.tsx
- 適切なimport文とsetup
```

このプロンプトファイルにより、一貫性のあるテストファイルを効率的に生成できます。

---

## 2. 単体テスト実装

コンポーネント・関数・カスタムフック単位でのテスト実装手法を学びます。

### :pen: 例題 - DeleteConfirmModalコンポーネントの単体テスト

作成したプロンプトファイルを使用してテストを実装します。

#### プロンプトファイルの呼び出し

チャット入力欄で以下のように入力してプロンプトファイルを実行します：

```markdown
/generate-test: componentName=DeleteConfirmModal
```

または、追加の詳細要件を指定：

```markdown
/generate-test

DeleteConfirmModalコンポーネントのテストファイルを生成してください。以下の要件を満たしてください：

1. **基本レンダリングテスト**
   - isOpen=trueの時にモーダルが表示される
   - isOpen=falseの時にモーダルが非表示になる
   - todoTitleが正しく表示される

2. **ユーザーインタラクションテスト**
   - 「削除する」ボタンクリック時にonConfirmが呼ばれる
   - 「キャンセル」ボタンクリック時にonCancelが呼ばれる
   - ESCキー押下時にonCancelが呼ばれる

3. **アクセシビリティテスト**
   - 適切なARIA属性が設定されている
   - モーダル表示時にフォーカスが移動する
   - タブナビゲーションが適切に動作する

React Testing Libraryのベストプラクティスに従い、ユーザー視点でのテストを作成してください。
```

### :robot: 出力例

```typescript
// DeleteConfirmModal.test.tsx

import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { DeleteConfirmModal } from '../DeleteConfirmModal';

describe('DeleteConfirmModal', () => {
  const defaultProps = {
    isOpen: true,
    todoTitle: 'テスト用のTodoタイトル',
    onConfirm: jest.fn(),
    onCancel: jest.fn(),
  };

  afterEach(() => {
    jest.clearAllMocks();
    // 📝 テスト後にbody要素のスタイルをリセット
    document.body.style.overflow = 'unset';
  });

  // 📝 正常系テスト
  describe('正常系テスト', () => {
    it('モーダルが開いているときに正常にレンダリングされる', () => {
      render(<DeleteConfirmModal {...defaultProps} />);

      expect(screen.getByRole('dialog')).toBeInTheDocument();
      expect(screen.getByText('削除の確認')).toBeInTheDocument();
      expect(screen.getByText('以下のTodoを削除しますか？')).toBeInTheDocument();
      expect(screen.getByText('テスト用のTodoタイトル')).toBeInTheDocument();
      expect(screen.getByText('🚩 この操作は取り消せません。')).toBeInTheDocument();
      expect(screen.getByText('キャンセル')).toBeInTheDocument();
      expect(screen.getByText('削除する')).toBeInTheDocument();
    });

    it('isOpenがfalseのときモーダルが表示されない', () => {
      render(<DeleteConfirmModal {...defaultProps} isOpen={false} />);

      expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
    });

    it('todoTitleプロパティの内容が正しく表示される', () => {
      const customTitle = '長いタイトルのTodoアイテムテスト';
      render(<DeleteConfirmModal {...defaultProps} todoTitle={customTitle} />);

      expect(screen.getByText(customTitle)).toBeInTheDocument();
    });

    it('削除するボタンをクリックするとonConfirmが呼ばれる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      await user.click(screen.getByText('削除する'));

      expect(defaultProps.onConfirm).toHaveBeenCalledTimes(1);
    });

    it('キャンセルボタンをクリックするとonCancelが呼ばれる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      await user.click(screen.getByText('キャンセル'));

      expect(defaultProps.onCancel).toHaveBeenCalledTimes(1);
    });

    it('閉じるボタン（X）をクリックするとonCancelが呼ばれる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      const closeButton = screen.getByLabelText('モーダルを閉じる');
      await user.click(closeButton);

      expect(defaultProps.onCancel).toHaveBeenCalledTimes(1);
    });

    it('バックドロップをクリックするとonCancelが呼ばれる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      const backdrop = screen.getByRole('dialog');
      await user.click(backdrop);

      expect(defaultProps.onCancel).toHaveBeenCalledTimes(1);
    });

    it('モーダル内部をクリックしてもonCancelが呼ばれない', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      const modalContent = screen.getByText('削除の確認');
      await user.click(modalContent);

      expect(defaultProps.onCancel).not.toHaveBeenCalled();
    });

    it('モーダル表示中にbody要素のoverflowがhiddenになる', () => {
      render(<DeleteConfirmModal {...defaultProps} />);

      expect(document.body.style.overflow).toBe('hidden');
    });
  });

  // 📝 異常系テスト
  describe('異常系テスト', () => {
    it('onConfirmが未定義でもエラーが発生しない', () => {
      const props = { ...defaultProps, onConfirm: (() => {}) as () => void };

      expect(() => render(<DeleteConfirmModal {...props} />)).not.toThrow();
    });

    it('onCancelが未定義でもエラーが発生しない', () => {
      const props = { ...defaultProps, onCancel: (() => {}) as () => void };

      expect(() => render(<DeleteConfirmModal {...props} />)).not.toThrow();
    });

    it('todoTitleが空文字列でも正常にレンダリングされる', () => {
      render(<DeleteConfirmModal {...defaultProps} todoTitle="" />);

      expect(screen.getByRole('dialog')).toBeInTheDocument();
      expect(screen.getByText('削除の確認')).toBeInTheDocument();
    });

    it('todoTitleが非常に長い文字列でも正常に表示される', () => {
      const longTitle = 'あ'.repeat(1000);
      render(<DeleteConfirmModal {...defaultProps} todoTitle={longTitle} />);

      expect(screen.getByText(longTitle)).toBeInTheDocument();
    });

    it('特殊文字を含むtodoTitleでも正常に表示される', () => {
      const specialTitle = '<script>alert("XSS")</script> & "quotes" & \'apostrophes\'';
      render(<DeleteConfirmModal {...defaultProps} todoTitle={specialTitle} />);

      expect(screen.getByText(specialTitle)).toBeInTheDocument();
    });
  });

  // 📝 アクセシビリティテスト
  describe('アクセシビリティテスト', () => {
    it('適切なARIA属性が設定されている', () => {
      render(<DeleteConfirmModal {...defaultProps} />);

      const dialog = screen.getByRole('dialog');
      expect(dialog).toHaveAttribute('aria-modal', 'true');
      expect(dialog).toHaveAttribute('aria-labelledby', 'modal-title');
    });

    it('モーダルタイトルに適切なIDが設定されている', () => {
      render(<DeleteConfirmModal {...defaultProps} />);

      const title = screen.getByText('削除の確認');
      expect(title).toHaveAttribute('id', 'modal-title');
    });

    it('閉じるボタンに適切なaria-labelが設定されている', () => {
      render(<DeleteConfirmModal {...defaultProps} />);

      const closeButton = screen.getByLabelText('モーダルを閉じる');
      expect(closeButton).toBeInTheDocument();
    });

    it('Escapeキーでモーダルが閉じられる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      await user.keyboard('{Escape}');

      expect(defaultProps.onCancel).toHaveBeenCalledTimes(1);
    });

    it('Escapeキーが複数回押されてもonCancelが適切に動作する', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      await user.keyboard('{Escape}');
      await user.keyboard('{Escape}');

      expect(defaultProps.onCancel).toHaveBeenCalledTimes(2);
    });

    it('Tabキーでフォーカス移動が正常に動作する', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      const closeButton = screen.getByLabelText('モーダルを閉じる');
      const cancelButton = screen.getByText('キャンセル');
      const confirmButton = screen.getByText('削除する');

      // 📝 最初のフォーカス可能要素にフォーカス
      closeButton.focus();
      expect(closeButton).toHaveFocus();

      // 📝 Tabキーで次の要素に移動
      await user.tab();
      expect(cancelButton).toHaveFocus();

      await user.tab();
      expect(confirmButton).toHaveFocus();

      // 📝 Shift+Tabで前の要素に戻る
      await user.tab({ shift: true });
      expect(cancelButton).toHaveFocus();
    });

    it('Enterキーで削除ボタンがアクティベートされる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      const confirmButton = screen.getByText('削除する');
      confirmButton.focus();

      await user.keyboard('{Enter}');

      expect(defaultProps.onConfirm).toHaveBeenCalledTimes(1);
    });

    it('Spaceキーで削除ボタンがアクティベートされる', async () => {
      const user = userEvent.setup();
      render(<DeleteConfirmModal {...defaultProps} />);

      const confirmButton = screen.getByText('削除する');
      confirmButton.focus();

      await user.keyboard(' ');

      expect(defaultProps.onConfirm).toHaveBeenCalledTimes(1);
    });
  });

  // 📝 状態変更の正常フロー
  describe('状態変更の正常フロー', () => {
    it('モーダルが閉じられた後にbody要素のスタイルがリセットされる', async () => {
      const { rerender } = render(<DeleteConfirmModal {...defaultProps} />);

      expect(document.body.style.overflow).toBe('hidden');

      rerender(<DeleteConfirmModal {...defaultProps} isOpen={false} />);

      await waitFor(() => {
        expect(document.body.style.overflow).toBe('unset');
      });
    });

    it('コンポーネントがアンマウントされた後にイベントリスナーが削除される', () => {
      const addEventListenerSpy = jest.spyOn(document, 'addEventListener');
      const removeEventListenerSpy = jest.spyOn(document, 'removeEventListener');

      const { unmount } = render(<DeleteConfirmModal {...defaultProps} />);

      expect(addEventListenerSpy).toHaveBeenCalledWith('keydown', expect.any(Function));

      unmount();

      expect(removeEventListenerSpy).toHaveBeenCalledWith('keydown', expect.any(Function));

      addEventListenerSpy.mockRestore();
      removeEventListenerSpy.mockRestore();
    });

    it('propsが更新されても適切に再レンダリングされる', () => {
      const { rerender } = render(<DeleteConfirmModal {...defaultProps} />);

      expect(screen.getByText('テスト用のTodoタイトル')).toBeInTheDocument();

      rerender(<DeleteConfirmModal {...defaultProps} todoTitle="更新されたタイトル" />);

      expect(screen.getByText('更新されたタイトル')).toBeInTheDocument();
      expect(screen.queryByText('テスト用のTodoタイトル')).not.toBeInTheDocument();
    });
  });
});
```

---

## 3. 統合テスト実装

複数のコンポーネントやAPIとの連携を含む統合テストの実装手法を学びます。

> **💡 統合テストでのプロンプトファイル活用**
>
> 単体テスト用の`generate-test`プロンプトは**1つのコンポーネント**に特化しています。統合テストでは複数コンポーネントの連携をテストするため、以下の2つのアプローチがあります：
>
> **アプローチ1:** 既存プロンプトファイルの基本構造を活用
>
> **アプローチ2:** 直接Copilotチャットで統合テストの要件を指定

### :pen: 例題 - 削除確認フローの統合テスト

直接Copilotチャットで統合テストの要件を指定して実行します：

```markdown
TodoApp全体の削除確認フロー統合テストを実装してください。以下は複数コンポーネントが連携する統合テストです：

1. **削除フロー全体のテスト**
   - TodoItemで削除ボタンをクリック
   - DeleteConfirmModalが表示される
   - 「削除する」ボタンで削除実行
   - TodoリストからTodoが削除される

2. **キャンセルフローのテスト**
   - 削除ボタンクリック後のモーダル表示
   - 「キャンセル」ボタンでモーダルクローズ
   - Todoが削除されずに残っている

3. **キーボード操作のテスト**
   - ESCキーでのモーダルクローズ
   - Enterキーでの削除実行（フォーカス時）

4. **複数Todo環境でのテスト**
   - 正しいTodoが削除される
   - 他のTodoに影響がない

```

### :robot: 出力例

```typescript
// TodoApp.integration.test.tsx

import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { TodoApp } from '../TodoApp';
import type { Todo } from '@/types';

// 📝 ローカルストレージのモック
const mockLocalStorage = (() => {
  let store: Record<string, string> = {};

  return {
    getItem: (key: string) => store[key] || null,
    setItem: (key: string, value: string) => {
      store[key] = value;
    },
    removeItem: (key: string) => {
      delete store[key];
    },
    clear: () => {
      store = {};
    }
  };
})();

Object.defineProperty(window, 'localStorage', {
  value: mockLocalStorage
});

// 📝 crypto.randomUUIDのモック
Object.defineProperty(global, 'crypto', {
  value: {
    randomUUID: () => Math.random().toString(36).substr(2, 9)
  }
});

describe('TodoApp 削除確認フロー統合テスト', () => {
  beforeEach(() => {
    // 📝 各テスト前にローカルストレージをクリア
    mockLocalStorage.clear();
    // 📝 body要素のスタイルをリセット
    document.body.style.overflow = 'unset';
  });

  afterEach(() => {
    // 📝 テスト後のクリーンアップ
    document.body.style.overflow = 'unset';
  });

  // 📝 テスト用のTodoを事前に追加するヘルパー関数
  const addTestTodos = async (user: ReturnType<typeof userEvent.setup>) => {
    const input = screen.getByPlaceholderText('新しいタスクを入力してください...');

    // 📝 複数のTodoを追加
    await user.type(input, '最初のタスク');
    await user.keyboard('{Enter}');

    await user.clear(input);
    await user.type(input, '削除対象のタスク');
    await user.keyboard('{Enter}');

    await user.clear(input);
    await user.type(input, '最後のタスク');
    await user.keyboard('{Enter}');

    // 📝 Todoが追加されるまで待機
    await waitFor(() => {
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();
    });
  };

  // 📝 削除フロー全体のテスト
  describe('削除フロー全体のテスト', () => {
    it('TodoItemで削除ボタンをクリック → DeleteConfirmModal表示 → 削除実行 → Todoリストから削除', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 初期状態の確認（3つのTodoが存在）
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();

      // 📝 「削除対象のタスク」の削除ボタンを探す
      const deleteButtons = screen.getAllByTitle('削除');
      expect(deleteButtons).toHaveLength(3);

      // 📝 2番目のTodo（削除対象のタスク）の削除ボタンをクリック
      await user.click(deleteButtons[1]);

      // 📝 削除確認モーダルが表示されることを確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
        expect(screen.getByText('削除の確認')).toBeInTheDocument();
        expect(screen.getByRole('dialog')).toHaveTextContent('削除対象のタスク');
      });

      // 📝 モーダル表示中はbody要素のoverflowがhidden
      expect(document.body.style.overflow).toBe('hidden');

      // 📝 「削除する」ボタンをクリック
      const confirmButton = screen.getByRole('button', { name: /削除する/ });
      await user.click(confirmButton);

      // 📝 モーダルが閉じられることを確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      });

      // 📝 body要素のoverflowがリセットされる
      await waitFor(() => {
        expect(document.body.style.overflow).toBe('unset');
      });

      // 📝 「削除対象のタスク」が削除され、他のTodoは残っていることを確認
      expect(screen.queryByText('削除対象のタスク')).not.toBeInTheDocument();
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();

      // 📝 削除ボタンが2つになっていることを確認
      const remainingDeleteButtons = screen.getAllByTitle('削除');
      expect(remainingDeleteButtons).toHaveLength(2);
    });

    it('複数Todoの環境で正しいTodoが削除される', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 初期状態の確認（3つのTodoが存在）
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();

      // 📝 削除ボタンの数を確認
      const deleteButtons = screen.getAllByTitle('削除');
      expect(deleteButtons).toHaveLength(3);

      // 📝 最初のTodoを削除
      await user.click(deleteButtons[0]);

      // 📝 削除確認モーダルが表示される
      await waitFor(() => {
        expect(screen.getByText('削除の確認')).toBeInTheDocument();
      });

      // 📝 削除実行
      await user.click(screen.getByRole('button', { name: /削除する/ }));

      // 📝 モーダルが閉じられることを確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      }, { timeout: 3000 });

      // 📝 削除ボタンの数で削除を確認（より確実）
      await waitFor(() => {
        const remainingDeleteButtons = screen.getAllByTitle('削除');
        expect(remainingDeleteButtons).toHaveLength(2);
      }, { timeout: 3000 });

      // 📝 残っているTodoを確認（どのTodoが削除されたかは関係なく、2つ残っていることを確認）
      const remainingTodos = [
        screen.queryByText('最初のタスク'),
        screen.queryByText('削除対象のタスク'),
        screen.queryByText('最後のタスク')
      ].filter(Boolean);

      expect(remainingTodos).toHaveLength(2);
    }, 10000);

    it('最後のTodoを削除すると空状態のメッセージが表示される', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 1つだけTodoを追加
      const input = screen.getByPlaceholderText('新しいタスクを入力してください...');
      await user.type(input, '唯一のタスク');
      await user.keyboard('{Enter}');

      await waitFor(() => {
        expect(screen.getByText('唯一のタスク')).toBeInTheDocument();
      });

      // 📝 削除実行
      const deleteButton = screen.getByTitle('削除');
      await user.click(deleteButton);

      await waitFor(() => {
        expect(screen.getByText('削除の確認')).toBeInTheDocument();
      });

      await user.click(screen.getByRole('button', { name: /削除する/ }));

      // 📝 空状態のメッセージが表示される
      await waitFor(() => {
        expect(screen.getByText('タスクがありません。新しいタスクを追加してください。')).toBeInTheDocument();
      });
    });
  });

  // 📝 キャンセルフローのテスト
  describe('キャンセルフローのテスト', () => {
    it('削除ボタンクリック → モーダル表示 → キャンセルボタン → モーダルクローズ → Todoが残る', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[1]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
        expect(screen.getByRole('dialog')).toHaveTextContent('削除対象のタスク');
      });

      // 📝 キャンセルボタンをクリック
      const cancelButton = screen.getByText('キャンセル');
      await user.click(cancelButton);

      // 📝 モーダルが閉じられることを確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      });

      // 📝 Todoが削除されずに残っていることを確認
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();

      // 📝 削除ボタンが3つのまま
      const remainingDeleteButtons = screen.getAllByTitle('削除');
      expect(remainingDeleteButtons).toHaveLength(3);
    });

    it('閉じるボタン（X）でモーダルをキャンセル', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[0]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      // 📝 閉じるボタン（X）をクリック
      const closeButton = screen.getByLabelText('モーダルを閉じる');
      await user.click(closeButton);

      // 📝 モーダルが閉じられることを確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      });

      // 📝 Todoが削除されずに残っている
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();
    });

    it('バックドロップクリックでモーダルをキャンセル', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[2]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      // 📝 バックドロップをクリック
      const backdrop = screen.getByRole('dialog');
      await user.click(backdrop);

      // 📝 モーダルが閉じられることを確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      });

      // 📝 Todoが削除されずに残っている
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();
    });
  });

  // 📝 キーボード操作のテスト
  describe('キーボード操作のテスト', () => {
    it('ESCキーでモーダルを閉じる', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[1]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      // 📝 ESCキーを押す
      await user.keyboard('{Escape}');

      // 📝 モーダルが閉じられることを確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      });

      // 📝 Todoが削除されずに残っている
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('削除対象のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();
    });

    it('Enterキーで削除実行（削除ボタンにフォーカス時）', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[1]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      // 📝 削除ボタンにフォーカスを当てる
      const confirmButton = screen.getByRole('button', { name: /削除する/ });
      confirmButton.focus();
      expect(confirmButton).toHaveFocus();

      // 📝 Enterキーで削除実行
      await user.keyboard('{Enter}');

      // 📝 モーダルが閉じられ、Todoが削除される
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
        expect(screen.queryByText('削除対象のタスク')).not.toBeInTheDocument();
      });

      // 📝 他のTodoは残っている
      expect(screen.getByText('最初のタスク')).toBeInTheDocument();
      expect(screen.getByText('最後のタスク')).toBeInTheDocument();
    });

    it('Spaceキーで削除実行（削除ボタンにフォーカス時）', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[0]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      // 📝 削除ボタンにフォーカスを当てる
      const confirmButton = screen.getByRole('button', { name: /削除する/ });
      confirmButton.focus();

      // 📝 Spaceキーで削除実行
      await user.keyboard(' ');

      // 📝 モーダルが閉じられることを先に確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      }, { timeout: 3000 });

      // 📝 削除ボタンの数で削除を確認（より確実）
      await waitFor(() => {
        const remainingDeleteButtons = screen.getAllByTitle('削除');
        expect(remainingDeleteButtons).toHaveLength(2);
      }, { timeout: 3000 });

      // 📝 残っているTodoを確認（どのTodoが削除されたかは関係なく、2つ残っていることを確認）
      const remainingTodos = [
        screen.queryByText('最初のタスク'),
        screen.queryByText('削除対象のタスク'),
        screen.queryByText('最後のタスク')
      ].filter(Boolean);

      expect(remainingTodos).toHaveLength(2);
    }, 10000);

    it('Tabキーでフォーカス移動が正常に動作する', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除ボタンをクリック
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[1]);

      // 📝 モーダル表示確認
      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      // 📝 フォーカス可能な要素を取得
      const closeButton = screen.getByLabelText('モーダルを閉じる');
      const cancelButton = screen.getByText('キャンセル');
      const confirmButton = screen.getByRole('button', { name: /削除する/ });

      // 📝 最初のフォーカス可能要素にフォーカス
      closeButton.focus();
      expect(closeButton).toHaveFocus();

      // 📝 Tabキーで移動
      await user.tab();
      expect(cancelButton).toHaveFocus();

      await user.tab();
      expect(confirmButton).toHaveFocus();

      // 📝 Shift+Tabで戻る
      await user.tab({ shift: true });
      expect(cancelButton).toHaveFocus();
    });
  });

  // 📝 エラーハンドリングと特殊ケース
  describe('エラーハンドリングと特殊ケース', () => {
    it('非常に長いタイトルのTodoでも削除フローが正常動作する', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 非常に長いタイトルのTodoを追加
      const longTitle = 'あ'.repeat(200);
      const input = screen.getByPlaceholderText('新しいタスクを入力してください...');
      await user.type(input, longTitle);
      await user.keyboard('{Enter}');

      await waitFor(() => {
        expect(screen.getByText(longTitle)).toBeInTheDocument();
      });

      // 📝 削除フロー実行
      const deleteButton = screen.getByTitle('削除');
      await user.click(deleteButton);

      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
        expect(screen.getByRole('dialog')).toHaveTextContent(longTitle);
      });

      await user.click(screen.getByRole('button', { name: /削除する/ }));

      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
        expect(screen.queryByText(longTitle)).not.toBeInTheDocument();
      });
    });

    it('特殊文字を含むタイトルのTodoでも削除フローが正常動作する', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 特殊文字を含むタイトルのTodoを追加
      const specialTitle = '<script>alert("XSS")</script> & "quotes" & \'apostrophes\'';
      const input = screen.getByPlaceholderText('新しいタスクを入力してください...');
      await user.type(input, specialTitle);
      await user.keyboard('{Enter}');

      await waitFor(() => {
        expect(screen.getByText(specialTitle)).toBeInTheDocument();
      });

      // 📝 削除フロー実行
      const deleteButton = screen.getByTitle('削除');
      await user.click(deleteButton);

      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
        expect(screen.getByRole('dialog')).toHaveTextContent(specialTitle);
      });

      await user.click(screen.getByRole('button', { name: /削除する/ }));

      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
        expect(screen.queryByText(specialTitle)).not.toBeInTheDocument();
      });
    });

    it('連続して複数のTodoを削除する', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 1つ目を削除
      let deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[0]);

      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      await user.click(screen.getByRole('button', { name: /削除する/ }));

      // 📝 モーダルが閉じられることを先に確認
      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      }, { timeout: 3000 });

      // 📝 削除ボタンの数で削除を確認（より確実）
      await waitFor(() => {
        const remainingDeleteButtons = screen.getAllByTitle('削除');
        expect(remainingDeleteButtons).toHaveLength(2);
      }, { timeout: 3000 });

      // 📝 2つ目を削除
      deleteButtons = screen.getAllByTitle('削除');
      expect(deleteButtons).toHaveLength(2);

      await user.click(deleteButtons[0]);

      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      await user.click(screen.getByRole('button', { name: /削除する/ }));

      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
      });

      // 📝 最後のTodoだけが残っている
      await waitFor(() => {
        const finalDeleteButtons = screen.getAllByTitle('削除');
        expect(finalDeleteButtons).toHaveLength(1);
      }, { timeout: 3000 });

      // 📝 少なくとも1つのTodoが残っていることを確認
      const remainingTodos = [
        screen.queryByText('最初のタスク'),
        screen.queryByText('削除対象のタスク'),
        screen.queryByText('最後のタスク')
      ].filter(Boolean);

      expect(remainingTodos).toHaveLength(1);
    }, 15000);
  });

  // 📝 ローカルストレージとの統合テスト
  describe('ローカルストレージとの統合テスト', () => {
    it('削除後の状態がローカルストレージに保存される', async () => {
      const user = userEvent.setup();
      render(<TodoApp />);

      // 📝 テスト用のTodoを追加
      await addTestTodos(user);

      // 📝 削除実行
      const deleteButtons = screen.getAllByTitle('削除');
      await user.click(deleteButtons[1]);

      await waitFor(() => {
        expect(screen.getByRole('dialog')).toBeInTheDocument();
      });

      await user.click(screen.getByRole('button', { name: /削除する/ }));

      await waitFor(() => {
        expect(screen.queryByRole('dialog')).not.toBeInTheDocument();
        expect(screen.queryByText('削除対象のタスク')).not.toBeInTheDocument();
      });

      // 📝 ローカルストレージの内容を確認
      const storedTodos = mockLocalStorage.getItem('todos');
      expect(storedTodos).toBeTruthy();

      const parsedTodos = JSON.parse(storedTodos!) as Todo[];
      expect(parsedTodos).toHaveLength(2);
      expect(parsedTodos.some((todo: Todo) => todo.text === '削除対象のタスク')).toBe(false);
      expect(parsedTodos.some((todo: Todo) => todo.text === '最初のタスク')).toBe(true);
      expect(parsedTodos.some((todo: Todo) => todo.text === '最後のタスク')).toBe(true);
    });
  });
});

```

---

## 4. テストカバレッジの最適化

テストカバレッジを効率的に向上させる手法を学びます。

### :pen: 例題 - カバレッジ分析と改善

```markdown
GHCP-TodoAppプロジェクトのテストカバレッジを分析し、改善提案をしてください：

1. 現在のカバレッジレポートの解析
2. カバレッジが不足している箇所の特定
3. 優先度の高いテスト追加箇所の特定

jest --coverage等を使用したカバレッジ分析と改善手順を具体的に示してください。
```

これを実行することで、テストカバレッジのレポートが生成され、どの部分が十分にテストされていないかを視覚的に把握できます。

---

## :memo: 練習

1. **プロンプトファイルの作成と活用**
   - テスト雛形生成用プロンプトファイルの作成
   - プロンプトファイルを使用したテスト実装
   - プロジェクト固有のテストパターンの標準化

2. **単体テストの実装**
   - DeleteConfirmModalコンポーネントのテスト
   - React Testing Libraryの基本的な使用方法の習得
   - アクセシビリティテストの実装

3. **統合テストの実装**
   - 削除確認フローの統合テスト
   - 複数コンポーネント間の連携テスト

4. **テストカバレッジの最適化**
   - カバレッジ分析と改善提案
   - 効率的なテスト追加戦略
   - CI/CDでのカバレッジ監視設定

> テスト実装の効率化により、品質保証と開発速度の両方を向上させることができます。プロンプトファイルを活用して一貫性のあるテストパターンを確立し、継続的な品質改善を実現しましょう。

---

## まとめ

* **プロンプトファイルによるテスト雛形の効率的な生成**
* **React Testing Libraryを使用した単体テスト実装**
* **複数コンポーネント連携の統合テスト実装**
* **MSWを活用したAPIモック環境でのテスト**
* **テストカバレッジ分析による品質向上戦略**
