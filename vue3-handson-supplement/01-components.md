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

#### 子コンポーネント（MyCard.vue）

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

#### 親コンポーネントでPropsを渡す

```vue
<!-- ParentComponent.vue -->
<template>
  <div>
    <!-- Propsとしてデータを渡す -->
    <MyCard 
      title="商品名" 
      description="商品の説明文です" 
    />
    
    <!-- 変数を使って渡すことも可能 -->
    <MyCard 
      :title="productName" 
      :description="productDescription" 
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import MyCard from './MyCard.vue'

const productName = ref('ノートパソコン')
const productDescription = ref('高性能なノートパソコンです')
</script>
```

## Emits - 子から親へのイベント送信

親コンポーネントから子コンポーネントにデータを渡すのがPropsなら、子コンポーネントから親コンポーネントにイベントを送信するのが**Emits**です。

### 基本的な使い方

```vue
<!-- Button.vue（子コンポーネント） -->
<template>
  <button @click="handleClick">
    <slot></slot>
  </button>
</template>

<script setup lang="ts">
// イベントの型を定義
const emit = defineEmits<{
  click: [event: MouseEvent]
}>()

const handleClick = (event: MouseEvent) => {
  // 親コンポーネントにclickイベントを送信
  emit('click', event)
}
</script>
```

```vue
<!-- ParentComponent.vue（親コンポーネント） -->
<template>
  <div>
    <p>クリック回数: {{ clickCount }}</p>
    <!-- 子コンポーネントのイベントを受け取る -->
    <Button @click="handleButtonClick">クリック</Button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import Button from './Button.vue'

const clickCount = ref(0)

const handleButtonClick = (event: MouseEvent) => {
  clickCount.value++
  console.log('ボタンがクリックされました', event)
}
</script>
```

### カスタムイベント

Propsでデータを受け取り、Emitsでイベントを送信する例：

```vue
<!-- ProductCard.vue（子コンポーネント） -->
<template>
  <div class="product-card">
    <h3>{{ product.name }}</h3>
    <p>{{ product.price }}円</p>
    <button @click="handleAddToCart">カートに追加</button>
  </div>
</template>

<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
}

interface Props {
  product: Product
}

defineProps<Props>()

// カスタムイベントを定義
const emit = defineEmits<{
  'add-to-cart': [product: Product]
}>()

const handleAddToCart = () => {
  // 親コンポーネントにadd-to-cartイベントを送信
  emit('add-to-cart', props.product)
}
</script>
```

```vue
<!-- ProductList.vue（親コンポーネント） -->
<template>
  <div>
    <h1>商品一覧</h1>
    <div v-for="product in products" :key="product.id">
      <ProductCard 
        :product="product" 
        @add-to-cart="handleAddToCart"
      />
    </div>
    <p>カート内の商品数: {{ cartItems.length }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import ProductCard from './ProductCard.vue'

interface Product {
  id: number
  name: string
  price: number
}

const products = ref<Product[]>([
  { id: 1, name: 'ノートパソコン', price: 50000 },
  { id: 2, name: 'マウス', price: 2000 }
])

const cartItems = ref<Product[]>([])

const handleAddToCart = (product: Product) => {
  cartItems.value.push(product)
  console.log('カートに追加:', product.name)
}
</script>
```

### 複数のイベントを定義

```vue
<!-- FormField.vue -->
<template>
  <div>
    <label>{{ label }}</label>
    <input 
      v-model="inputValue"
      @input="handleInput"
      @blur="handleBlur"
      @focus="handleFocus"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Props {
  label: string
  modelValue: string
}

const props = defineProps<Props>()

// 複数のイベントを定義
const emit = defineEmits<{
  'update:modelValue': [value: string]
  input: [value: string]
  blur: []
  focus: []
}>()

const inputValue = ref(props.modelValue)

const handleInput = () => {
  emit('update:modelValue', inputValue.value)
  emit('input', inputValue.value)
}

const handleBlur = () => {
  emit('blur')
}

const handleFocus = () => {
  emit('focus')
}
</script>
```

### defineEmitsの書き方

`defineEmits`は、コンポーネントが発行するイベントの型を定義します。

```typescript
// 基本的な書き方
const emit = defineEmits<{
  イベント名: [引数1の型, 引数2の型, ...]
}>()

// 例1: 引数なし
const emit = defineEmits<{
  close: []
}>()

// 例2: 1つの引数
const emit = defineEmits<{
  update: [value: string]
}>()

// 例3: 複数の引数
const emit = defineEmits<{
  submit: [name: string, age: number]
}>()

// 例4: 複数のイベント
const emit = defineEmits<{
  click: [event: MouseEvent]
  change: [value: string]
  submit: [data: FormData]
}>()
```

### PropsとEmitsの関係

- **Props**: 親 → 子（データを渡す）
- **Emits**: 子 → 親（イベントを送信）

```vue
<!-- 親コンポーネント -->
<template>
  <ChildComponent 
    :message="parentMessage"
    @update-message="handleUpdate"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import ChildComponent from './ChildComponent.vue'

const parentMessage = ref('親からのメッセージ')

const handleUpdate = (newMessage: string) => {
  parentMessage.value = newMessage
}
</script>
```

```vue
<!-- 子コンポーネント -->
<template>
  <div>
    <p>親からのメッセージ: {{ message }}</p>
    <button @click="updateMessage">メッセージを更新</button>
  </div>
</template>

<script setup lang="ts">
interface Props {
  message: string
}

const props = defineProps<Props>()

const emit = defineEmits<{
  'update-message': [message: string]
}>()

const updateMessage = () => {
  // 親コンポーネントに新しいメッセージを送信
  emit('update-message', '子から更新されたメッセージ')
}
</script>
```

## まとめ

- コンポーネントは再利用可能なUIの部品
- テンプレート、スクリプト、スタイルの3つで構成される
- **Props**: 親から子にデータを渡す
- **Emits**: 子から親にイベントを送信する
- コンポーネントを使うことで、コードの再利用性と保守性が向上する

## 参考資料

- [Vue.js - コンポーネントの基礎](https://ja.vuejs.org/guide/essentials/component-basics.html)
- [Vue.js - Props](https://ja.vuejs.org/guide/components/props.html)
- [Vue.js - イベント](https://ja.vuejs.org/guide/components/events.html)

