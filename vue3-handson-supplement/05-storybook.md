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

### Storyの作成

Storyは、コンポーネントの特定の状態を表す設定です。

```typescript
// MyButton.stories.ts
import type { Meta, StoryObj } from '@storybook/vue3'
import MyButton from './MyButton.vue'

// メタデータ（コンポーネントの情報）
const meta: Meta<typeof MyButton> = {
  title: 'Components/MyButton',
  component: MyButton,
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary']
    }
  }
}

export default meta
type Story = StoryObj<typeof MyButton>

// デフォルトのStory
export const Default: Story = {
  args: {
    label: 'ボタン',
    variant: 'primary'
  }
}

// セカンダリーバリアント
export const Secondary: Story = {
  args: {
    label: 'セカンダリーボタン',
    variant: 'secondary'
  }
}
```

### コンポーネントの表示

Storybookを起動すると、左サイドバーにStoryの一覧が表示されます。Storyを選択すると、そのコンポーネントが表示されます。

### コントロールパネル

画面下部のコントロールパネルで、Propsの値を変更できます。

- **Controls**: Propsの値を変更
- **Actions**: イベントのログを確認
- **Docs**: ドキュメントを表示

## 実践的な使用例

### 例1: ボタンコンポーネント

```vue
<!-- Button.vue -->
<template>
  <button 
    :class="['btn', `btn-${variant}`, { 'btn-disabled': disabled }]"
    :disabled="disabled"
    @click="$emit('click')"
  >
    {{ label }}
  </button>
</template>

<script setup lang="ts">
interface Props {
  label: string
  variant?: 'primary' | 'secondary' | 'danger'
  disabled?: boolean
}

withDefaults(defineProps<Props>(), {
  variant: 'primary',
  disabled: false
})

defineEmits<{
  click: []
}>()
</script>
```

```typescript
// Button.stories.ts
import type { Meta, StoryObj } from '@storybook/vue3'
import Button from './Button.vue'

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'danger']
    },
    disabled: {
      control: 'boolean'
    },
    click: { action: 'clicked' }
  }
}

export default meta
type Story = StoryObj<typeof Button>

export const Primary: Story = {
  args: {
    label: 'プライマリーボタン',
    variant: 'primary'
  }
}

export const Secondary: Story = {
  args: {
    label: 'セカンダリーボタン',
    variant: 'secondary'
  }
}

export const Disabled: Story = {
  args: {
    label: '無効なボタン',
    disabled: true
  }
}
```

### 例2: フォームコンポーネント

```typescript
// FormField.stories.ts
import type { Meta, StoryObj } from '@storybook/vue3'
import { ref } from 'vue'
import FormField from './FormField.vue'

const meta: Meta<typeof FormField> = {
  title: 'Components/FormField',
  component: FormField
}

export default meta
type Story = StoryObj<typeof FormField>

export const TextInput: Story = {
  render: () => ({
    components: { FormField },
    setup() {
      const value = ref('')
      return { value }
    },
    template: '<FormField v-model="value" label="名前" type="text" />'
  })
}

export const EmailInput: Story = {
  render: () => ({
    components: { FormField },
    setup() {
      const value = ref('')
      return { value }
    },
    template: '<FormField v-model="value" label="メールアドレス" type="email" />'
  })
}
```

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

