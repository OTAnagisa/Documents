# コンポーネント分割の考え方

## 簡単な説明

**コンポーネント分割**とは、大きなコンポーネントを小さな部品に分けることです。適切に分割することで、コードが読みやすくなり、再利用しやすくなり、保守しやすくなります。ただし、分割しすぎると逆に複雑になるので、適切なタイミングで分割することが重要です。

## いつコンポーネントを分割するか

### 分割すべきタイミング

1. **同じコードが繰り返されている**
   - 同じようなHTMLの構造が3回以上繰り返されている
   - 例: 商品カードが複数表示される場合

2. **コンポーネントが大きくなりすぎた**
   - 1つのコンポーネントが200行を超える
   - 複数の責任を持っている（表示、ロジック、API通信など）

3. **再利用したい機能がある**
   - 他の画面でも使いたい部品がある
   - 例: ボタン、モーダル、フォームなど

4. **テストしやすくしたい**
   - 特定の機能だけを独立してテストしたい

### 分割しなくても良い場合

- そのコンポーネントが1箇所でしか使われない
- コンポーネントが小さく、理解しやすい
- 分割すると逆に複雑になる

## 分割のベストプラクティス

### 1. 単一責任の原則

1つのコンポーネントは、1つの責任だけを持つべきです。

```vue
<!-- ❌ 悪い例: 複数の責任を持っている -->
<template>
  <div>
    <h1>商品一覧</h1>
    <div v-for="product in products" :key="product.id">
      <img :src="product.image" />
      <h3>{{ product.name }}</h3>
      <p>{{ product.price }}円</p>
      <button @click="addToCart(product)">カートに追加</button>
    </div>
    <div class="cart">
      <h2>カート</h2>
      <!-- カートの表示 -->
    </div>
  </div>
</template>
```

```vue
<!-- ✅ 良い例: 責任を分離 -->
<template>
  <div>
    <h1>商品一覧</h1>
    <ProductList :products="products" @add-to-cart="addToCart" />
    <ShoppingCart :items="cartItems" />
  </div>
</template>

<script setup lang="ts">
import ProductList from './ProductList.vue'
import ShoppingCart from './ShoppingCart.vue'
// ...
</script>
```

### 2. 再利用可能な部品を抽出

複数の場所で使われる可能性がある部品は、コンポーネントとして抽出します。

```vue
<!-- Button.vue - 再利用可能なボタンコンポーネント -->
<template>
  <button 
    :class="['btn', `btn-${variant}`, { 'btn-disabled': disabled }]"
    :disabled="disabled"
    @click="$emit('click')"
  >
    <slot></slot>
  </button>
</template>

<script setup lang="ts">
interface Props {
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

### 3. データと表示の分離

データの取得・管理と表示を分離します。

```vue
<!-- ProductPage.vue - データ管理 -->
<template>
  <div>
    <ProductList v-if="!loading" :products="products" />
    <LoadingSpinner v-else />
    <ErrorMessage v-if="error" :message="error" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import ProductList from './ProductList.vue'
import LoadingSpinner from './LoadingSpinner.vue'
import ErrorMessage from './ErrorMessage.vue'

const products = ref([])
const loading = ref(true)
const error = ref('')

onMounted(async () => {
  try {
    // APIからデータを取得
    const response = await fetch('/api/products')
    products.value = await response.json()
  } catch (e) {
    error.value = 'データの取得に失敗しました'
  } finally {
    loading.value = false
  }
})
</script>
```

```vue
<!-- ProductList.vue - 表示のみ -->
<template>
  <div class="product-list">
    <ProductCard 
      v-for="product in products" 
      :key="product.id"
      :product="product"
      @add-to-cart="$emit('add-to-cart', product)"
    />
  </div>
</template>

<script setup lang="ts">
import ProductCard from './ProductCard.vue'

interface Props {
  products: Product[]
}

defineProps<Props>()

defineEmits<{
  'add-to-cart': [product: Product]
}>()
</script>
```

## 分割の実践例

### 例1: 商品一覧ページ

#### 分割前（1つのコンポーネント）

```vue
<template>
  <div>
    <h1>商品一覧</h1>
    <div v-for="product in products" :key="product.id" class="product-card">
      <img :src="product.image" alt="product.name" />
      <h3>{{ product.name }}</h3>
      <p>{{ product.description }}</p>
      <p class="price">{{ product.price }}円</p>
      <button @click="addToCart(product)">カートに追加</button>
    </div>
  </div>
</template>
```

#### 分割後（複数のコンポーネント）

```vue
<!-- ProductPage.vue -->
<template>
  <div>
    <h1>商品一覧</h1>
    <ProductList :products="products" @add-to-cart="handleAddToCart" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import ProductList from './ProductList.vue'

const products = ref([...])

const handleAddToCart = (product: Product) => {
  // カートに追加する処理
}
</script>
```

```vue
<!-- ProductList.vue -->
<template>
  <div class="product-list">
    <ProductCard
      v-for="product in products"
      :key="product.id"
      :product="product"
      @add-to-cart="$emit('add-to-cart', $event)"
    />
  </div>
</template>

<script setup lang="ts">
import ProductCard from './ProductCard.vue'

interface Props {
  products: Product[]
}

defineProps<Props>()
defineEmits<{
  'add-to-cart': [product: Product]
}>()
</script>
```

```vue
<!-- ProductCard.vue -->
<template>
  <div class="product-card">
    <img :src="product.image" :alt="product.name" />
    <h3>{{ product.name }}</h3>
    <p>{{ product.description }}</p>
    <p class="price">{{ product.price }}円</p>
    <button @click="$emit('add-to-cart', product)">カートに追加</button>
  </div>
</template>

<script setup lang="ts">
interface Props {
  product: Product
}

defineProps<Props>()
defineEmits<{
  'add-to-cart': [product: Product]
}>()
</script>
```

### 例2: フォームの分割

```vue
<!-- UserForm.vue -->
<template>
  <form @submit.prevent="handleSubmit">
    <FormField 
      v-model="form.name"
      label="名前"
      type="text"
      required
    />
    <FormField 
      v-model="form.email"
      label="メールアドレス"
      type="email"
      required
    />
    <FormField 
      v-model="form.message"
      label="メッセージ"
      type="textarea"
    />
    <Button type="submit">送信</Button>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import FormField from './FormField.vue'
import Button from './Button.vue'

const form = ref({
  name: '',
  email: '',
  message: ''
})

const handleSubmit = () => {
  // 送信処理
}
</script>
```

## コンポーネントの命名規則

### ファイル名

- **パスカルケース**: `ProductCard.vue`, `UserProfile.vue`
- **意味のある名前**: `Card.vue`ではなく`ProductCard.vue`

### ディレクトリ構造

```
components/
  ├── common/          # 共通コンポーネント
  │   ├── Button.vue
  │   ├── Input.vue
  │   └── Modal.vue
  ├── product/         # 商品関連
  │   ├── ProductCard.vue
  │   └── ProductList.vue
  └── user/            # ユーザー関連
      ├── UserProfile.vue
      └── UserForm.vue
```

## 再利用性を高める方法

### 1. Propsで柔軟性を持たせる

```vue
<!-- Card.vue -->
<template>
  <div :class="['card', `card-${variant}`]">
    <div v-if="$slots.header" class="card-header">
      <slot name="header"></slot>
    </div>
    <div class="card-body">
      <slot></slot>
    </div>
    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer"></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  variant?: 'default' | 'primary' | 'success'
}

withDefaults(defineProps<Props>(), {
  variant: 'default'
})
</script>
```

### 2. スロットで内容をカスタマイズ可能にする

```vue
<!-- 使用例 -->
<Card variant="primary">
  <template #header>
    <h2>タイトル</h2>
  </template>
  
  コンテンツ
  
  <template #footer>
    <button>アクション</button>
  </template>
</Card>
```

## まとめ

- **分割のタイミング**: 繰り返しコード、大きすぎるコンポーネント、再利用したい機能
- **単一責任**: 1つのコンポーネントは1つの責任だけ
- **データと表示の分離**: データ管理と表示を分ける
- **再利用性**: Propsとスロットで柔軟性を持たせる
- **命名規則**: 意味のある名前を付ける

## 参考資料

- [Vue.js - コンポーネントの基礎](https://ja.vuejs.org/guide/essentials/component-basics.html)
- [Vue.js - Props](https://ja.vuejs.org/guide/components/props.html)
- [Vue.js - スロット](https://ja.vuejs.org/guide/components/slots.html)

