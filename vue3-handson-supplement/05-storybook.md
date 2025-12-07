# Storybookについて

## 簡単な説明

**Storybook**は、コンポーネントを独立して開発・確認・テストできるツールです。実際のアプリケーションとは別に、コンポーネント単体で動作を確認できます。デザイナーや開発者が、コンポーネントの見た目や動作を確認しやすくするためのツールです。

## Storybookとは

Storybookは、UIコンポーネントを開発・管理するためのオープンソースツールです。実際のアプリケーションを起動しなくても、コンポーネントを個別に表示・操作・テストできます。

### 主な機能

1. **コンポーネントの表示**: 様々な状態のコンポーネントを表示
2. **インタラクティブな操作**: コンポーネントを操作して動作を確認
3. **ドキュメント化**: コンポーネントの使い方をドキュメント化
4. **視覚的なテスト**: コンポーネントの見た目を確認
5. **アクセシビリティテスト**: アクセシビリティの問題を検出

## Storybookでできること

### 1. コンポーネントの確認

実際のアプリケーションとは別に、コンポーネント単体で動作を確認できます。

```
通常の開発フロー:
1. アプリを起動
2. 該当ページに移動
3. コンポーネントを確認

Storybookを使った開発フロー:
1. Storybookを起動
2. 直接コンポーネントを確認
```

### 2. 様々な状態の表示

同じコンポーネントを、異なるPropsや状態で表示できます。

```typescript
// Button.stories.ts
import Button from './Button.vue'

export default {
  component: Button,
  title: 'Components/Button'
}

export const Primary = {
  args: {
    label: 'プライマリーボタン',
    variant: 'primary'
  }
}

export const Secondary = {
  args: {
    label: 'セカンダリーボタン',
    variant: 'secondary'
  }
}

export const Disabled = {
  args: {
    label: '無効なボタン',
    disabled: true
  }
}
```

### 3. インタラクティブな操作

Storybookでは、コンポーネントを実際に操作できます。

- ボタンをクリック
- フォームに入力
- モーダルを開閉
- など

### 4. コントロール（Controls）

Propsの値を変更して、コンポーネントの見た目を確認できます。

```typescript
export default {
  component: Button,
  title: 'Components/Button',
  argTypes: {
    variant: {
      control: { type: 'select' },
      options: ['primary', 'secondary', 'danger']
    },
    size: {
      control: { type: 'select' },
      options: ['small', 'medium', 'large']
    },
    disabled: {
      control: { type: 'boolean' }
    }
  }
}
```

### 5. ドキュメントの自動生成

コンポーネントのPropsや使い方が自動的にドキュメント化されます。

## Storybookの基本的な使い方

### Storybookを開く

Storybookを起動すると、ブラウザで `http://localhost:6006` が開きます。左サイドバーにコンポーネントの一覧が表示されます。

### コンポーネントの確認

1. **左サイドバーからコンポーネントを選択**: コンポーネント名をクリックすると、そのコンポーネントが表示されます
2. **Storyを選択**: 同じコンポーネントでも、異なる状態（Story）を確認できます
   - 例: Buttonコンポーネントの「Primary」「Secondary」「Disabled」など

### コントロールパネルでPropsを変更

画面下部のコントロールパネルで、コンポーネントのPropsの値を変更して、見た目や動作を確認できます。

- **Controlsタブ**: Propsの値を変更（テキスト入力、セレクトボックス、チェックボックスなど）
- **Actionsタブ**: イベント（クリックなど）のログを確認
- **Docsタブ**: コンポーネントの使い方やPropsの説明を確認

### コードをコピー

Storybookでは、表示されているコンポーネントの使用例コードをコピーできます。コード例を確認して、実際のプロジェクトに貼り付けて使えます。

### 実際の使用例

1. 左サイドバーから「Button」コンポーネントを選択
2. 「Primary」のStoryを選択
3. コントロールパネルで「label」や「variant」の値を変更して、見た目の変化を確認
4. コード例をコピーして、自分のプロジェクトで使用

## Storybookのメリット

### 1. 開発効率の向上

- アプリ全体を起動しなくてもコンポーネントを確認できる
- 様々な状態を簡単に確認できる
- デザイナーと開発者の連携がしやすい

### 2. 品質の向上

- コンポーネント単体でテストできる
- 様々な状態を網羅的に確認できる
- アクセシビリティの問題を早期に発見できる

### 3. ドキュメント化

- コンポーネントの使い方が明確になる
- 新しいメンバーが理解しやすくなる
- デザインシステムとして機能する

## Storybookの起動方法

通常、プロジェクトにStorybookが設定されている場合、以下のコマンドで起動できます：

```bash
npm run storybook
# または
yarn storybook
```

ブラウザで `http://localhost:6006` が開き、Storybookの画面が表示されます。

## まとめ

- **Storybook**: コンポーネントを独立して開発・確認できるツール
- **Story**: コンポーネントの特定の状態を表す設定
- **コントロール**: Propsの値を変更してコンポーネントを確認
- **メリット**: 開発効率向上、品質向上、ドキュメント化
- **使い方**: Storyを作成して、Storybookで確認

## 参考資料

- [Storybook 公式ドキュメント](https://storybook.js.org/)
- [Storybook for Vue](https://storybook.js.org/docs/vue/get-started/introduction)

