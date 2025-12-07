# コンポーネントとは何か

## 簡単な説明

**コンポーネント**は、再利用可能なUIの部品です。HTMLの要素を組み合わせて、ボタンやフォーム、カードなどの機能を持った部品を作ることができます。コンポーネントを使うことで、同じコードを何度も書かずに、一度作った部品を様々な場所で使い回すことができます。

## なぜコンポーネントを使うのか

従来のHTML/CSS/JavaScriptでWebページを作る場合、同じような構造のHTMLを何度も書く必要がありました。例えば、商品一覧ページで商品カードを10個表示する場合、同じHTMLを10回書く必要があります。

コンポーネントを使うと：
- **再利用性**: 一度作ったコンポーネントを何度でも使える
- **保守性**: 一箇所を修正すれば、使っている全ての場所に反映される
- **可読性**: コードが整理されて読みやすくなる
- **テストしやすさ**: 個別のコンポーネントを独立してテストできる

## Vueコンポーネントの基本構造

Vueコンポーネントは、通常3つの部分で構成されます：

1. **テンプレート（Template）**: HTMLのような見た目を定義
2. **スクリプト（Script）**: ロジックやデータを定義
3. **スタイル（Style）**: CSSで見た目を装飾

### 基本的な例

```vue
<template>
  <div class="card">
    <h2>{{ title }}</h2>
    <p>{{ description }}</p>
    <button @click="handleClick">クリック</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// コンポーネントのデータ（状態）
const title = ref('カードのタイトル')
const description = ref('これはカードの説明です')

// メソッド（関数）
const handleClick = () => {
  console.log('ボタンがクリックされました')
}
</script>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 16px;
  border-radius: 4px;
}
</style>
```

## コンポーネントの種類

### 1. 単一ファイルコンポーネント（SFC）

上記の例のように、`.vue`ファイルにテンプレート、スクリプト、スタイルを全て含める形式です。Vue 3では最も一般的な形式です。

### 2. グローバルコンポーネント vs ローカルコンポーネント

- **グローバルコンポーネント**: アプリケーション全体で使えるコンポーネント
- **ローカルコンポーネント**: 特定のコンポーネント内でのみ使えるコンポーネント

通常は、必要な場所でのみインポートして使うローカルコンポーネントを使います。

## コンポーネントの使い方

### コンポーネントをインポートして使う

```vue
<template>
  <div>
    <!-- コンポーネントをHTMLタグのように使う -->
    <MyCard title="商品1" description="これは商品1です" />
    <MyCard title="商品2" description="これは商品2です" />
  </div>
</template>

<script setup lang="ts">
import MyCard from './components/MyCard.vue'
</script>
```

### Props（プロパティ）でデータを渡す

親コンポーネントから子コンポーネントにデータを渡すには、**Props**を使います。

```vue
<!-- MyCard.vue -->
<template>
  <div class="card">
    <h2>{{ title }}</h2>
    <p>{{ description }}</p>
  </div>
</template>

<script setup lang="ts">
// Propsの定義
interface Props {
  title: string
  description: string
}

const props = defineProps<Props>()
</script>
```

## コンポーネントの命名規則

- **パスカルケース**: コンポーネント名は大文字で始める（例: `MyCard`, `UserProfile`）
- **ファイル名**: コンポーネントファイルは通常、コンポーネント名と同じにする（例: `MyCard.vue`）

## 実際の例

Webアプリケーションでは、以下のようなコンポーネントがよく作られます：

- **Button**: ボタンコンポーネント
- **Input**: 入力フィールドコンポーネント
- **Card**: カードコンポーネント
- **Modal**: モーダルダイアログコンポーネント
- **Header**: ヘッダーコンポーネント
- **Footer**: フッターコンポーネント

これらを組み合わせることで、複雑なWebアプリケーションを構築できます。

## まとめ

- コンポーネントは再利用可能なUIの部品
- テンプレート、スクリプト、スタイルの3つで構成される
- Propsを使って親から子にデータを渡せる
- コンポーネントを使うことで、コードの再利用性と保守性が向上する

## 参考資料

- [Vue.js - コンポーネントの基礎](https://ja.vuejs.org/guide/essentials/component-basics.html)
- [Vue.js - Props](https://ja.vuejs.org/guide/components/props.html)

