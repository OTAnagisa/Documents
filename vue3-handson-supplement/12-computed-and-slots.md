# 算出プロパティとスロット

## 簡単な説明

**算出プロパティ（computed）**は、リアクティブなデータから計算された値を返すプロパティです。データが変更されると、自動的に再計算されます。**スロット（slots）**は、コンポーネントにコンテンツを渡すための仕組みです。これらを使うことで、より柔軟で再利用可能なコンポーネントを作れます。

## 算出プロパティ（computed）

### 基本的な使い方

`computed`は、リアクティブなデータから計算された値を返します。

```vue
<template>
  <div>
    <p>価格: {{ price }}円</p>
    <p>数量: {{ quantity }}</p>
    <p>合計: {{ total }}円</p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const price = ref(1000)
const quantity = ref(2)

// 算出プロパティ: priceとquantityが変更されると自動的に再計算される
const total = computed(() => {
  return price.value * quantity.value
})
</script>
```

### なぜcomputedを使うのか

通常の関数と違い、`computed`は結果をキャッシュします。依存するデータが変更されない限り、再計算されません。

```vue
<template>
  <div>
    <p>合計（computed）: {{ total }}</p>
    <p>合計（関数）: {{ getTotal() }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const price = ref(1000)
const quantity = ref(2)

// computed: キャッシュされる（priceやquantityが変わらない限り再計算されない）
const total = computed(() => {
  console.log('computedが実行されました')
  return price.value * quantity.value
})

// 関数: 毎回実行される
const getTotal = () => {
  console.log('関数が実行されました')
  return price.value * quantity.value
}
</script>
```

### 実践的な例

#### フィルタリング

```vue
<template>
  <div>
    <input v-model="searchQuery" placeholder="検索..." />
    
    <ul>
      <li v-for="item in filteredItems" :key="item.id">
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Item {
  id: number
  name: string
}

const items = ref<Item[]>([
  { id: 1, name: 'りんご' },
  { id: 2, name: 'バナナ' },
  { id: 3, name: 'オレンジ' }
])

const searchQuery = ref('')

// 検索クエリに基づいてフィルタリング
const filteredItems = computed(() => {
  if (!searchQuery.value) {
    return items.value
  }
  return items.value.filter(item =>
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})
</script>
```

#### ソート

```vue
<template>
  <div>
    <select v-model="sortOrder">
      <option value="asc">昇順</option>
      <option value="desc">降順</option>
    </select>
    
    <ul>
      <li v-for="item in sortedItems" :key="item.id">
        {{ item.name }} - {{ item.price }}円
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Product {
  id: number
  name: string
  price: number
}

const products = ref<Product[]>([
  { id: 1, name: 'りんご', price: 100 },
  { id: 2, name: 'バナナ', price: 80 },
  { id: 3, name: 'オレンジ', price: 120 }
])

const sortOrder = ref<'asc' | 'desc'>('asc')

// ソート順に基づいて並び替え
const sortedItems = computed(() => {
  const sorted = [...products.value].sort((a, b) => a.price - b.price)
  return sortOrder.value === 'asc' ? sorted : sorted.reverse()
})
</script>
```

## スロット（slots）

スロットは、コンポーネントにコンテンツを渡すための仕組みです。親コンポーネントから子コンポーネントに、HTMLやコンテンツを渡すことができます。

### 基本的なスロット

最もシンプルなスロットは、`<slot>`タグを使います。

```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <slot></slot>
  </div>
</template>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 16px;
  border-radius: 4px;
}
</style>
```

```vue
<!-- 使用例 -->
<template>
  <Card>
    <h2>タイトル</h2>
    <p>コンテンツ</p>
  </Card>
</template>

<script setup lang="ts">
import Card from './Card.vue'
</script>
```

この例では、`<Card>`タグの中に書いた内容（`<h2>`と`<p>`）が、`<slot>`の位置に表示されます。

### スロットのデフォルト内容

スロットにデフォルトの内容を指定できます。親コンポーネントから何も渡されない場合、デフォルトの内容が表示されます。

```vue
<!-- Button.vue -->
<template>
  <button class="btn">
    <slot>ボタン</slot>
  </button>
</template>
```

```vue
<!-- 使用例 -->
<template>
  <!-- デフォルトの内容が表示される -->
  <Button />
  
  <!-- カスタムの内容が表示される -->
  <Button>カスタムボタン</Button>
</template>
```

### 名前付きスロット

複数のスロットを使う場合、名前を付けることができます。これにより、異なる場所に異なるコンテンツを渡せます。

```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <div class="card-header">
      <slot name="header"></slot>
    </div>
    <div class="card-body">
      <slot></slot>
    </div>
    <div class="card-footer">
      <slot name="footer"></slot>
    </div>
  </div>
</template>

<style scoped>
.card {
  border: 1px solid #ccc;
  border-radius: 4px;
}

.card-header {
  padding: 16px;
  border-bottom: 1px solid #ccc;
  font-weight: bold;
}

.card-body {
  padding: 16px;
}

.card-footer {
  padding: 16px;
  border-top: 1px solid #ccc;
}
</style>
```

```vue
<!-- 使用例 -->
<template>
  <Card>
    <!-- #header は v-slot:header の省略形 -->
    <template #header>
      <h2>カードのタイトル</h2>
    </template>
    
    <!-- 名前のないスロットはデフォルトスロット -->
    <p>カードの本文です</p>
    
    <template #footer>
      <button>アクション</button>
    </template>
  </Card>
</template>
```

### スコープ付きスロット（Scoped Slots）

スコープ付きスロットは、子コンポーネントから親コンポーネントにデータを渡すための仕組みです。これにより、親コンポーネントで子コンポーネントのデータを使って、表示方法をカスタマイズできます。

#### 基本的なスコープ付きスロット

```vue
<!-- ProductList.vue -->
<template>
  <ul>
    <li v-for="product in products" :key="product.id">
      <!-- スロットにproductデータを渡す -->
      <slot :product="product"></slot>
    </li>
  </ul>
</template>

<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
}

interface Props {
  products: Product[]
}

defineProps<Props>()
</script>
```

```vue
<!-- 使用例 -->
<template>
  <ProductList :products="products">
    <!-- #default="{ product }" でスロットからデータを受け取る -->
    <template #default="{ product }">
      <div>
        <h3>{{ product.name }}</h3>
        <p>{{ product.price }}円</p>
      </div>
    </template>
  </ProductList>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import ProductList from './ProductList.vue'

const products = ref([
  { id: 1, name: 'ノートパソコン', price: 50000 },
  { id: 2, name: 'マウス', price: 2000 }
])
</script>
```

#### 複数のデータを渡す

スコープ付きスロットでは、複数のデータを渡すことができます。

```vue
<!-- Table.vue -->
<template>
  <table>
    <thead>
      <tr>
        <th v-for="column in columns" :key="column.key">
          {{ column.label }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in rows" :key="row.id">
        <td v-for="column in columns" :key="column.key">
          <!-- 複数のデータを渡す -->
          <slot 
            :row="row" 
            :column="column" 
            :index="index"
            :value="row[column.key]"
          ></slot>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script setup lang="ts">
interface Column {
  key: string
  label: string
}

interface Props {
  columns: Column[]
  rows: Record<string, any>[]
}

defineProps<Props>()
</script>
```

```vue
<!-- 使用例 -->
<template>
  <Table :columns="columns" :rows="users">
    <template #default="{ row, column, value }">
      <!-- 特定のカラムだけカスタマイズ -->
      <span v-if="column.key === 'status'" :class="`status-${value}`">
        {{ value }}
      </span>
      <span v-else>{{ value }}</span>
    </template>
  </Table>
</template>
```

#### 名前付きスコープ付きスロット

名前付きスロットでも、スコープ付きスロットを使えます。

```vue
<!-- ProductCard.vue -->
<template>
  <div class="product-card">
    <div class="product-image">
      <slot name="image" :product="product">
        <!-- デフォルトの画像 -->
        <img :src="product.image" :alt="product.name" />
      </slot>
    </div>
    <div class="product-info">
      <slot name="title" :product="product">
        <h3>{{ product.name }}</h3>
      </slot>
      <slot name="price" :product="product">
        <p>{{ product.price }}円</p>
      </slot>
      <slot name="actions" :product="product"></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
  image: string
}

interface Props {
  product: Product
}

defineProps<Props>()
</script>
```

```vue
<!-- 使用例 -->
<template>
  <ProductCard :product="product">
    <!-- 画像スロットをカスタマイズ -->
    <template #image="{ product }">
      <div class="custom-image">
        <img :src="product.image" :alt="product.name" />
        <span class="badge">NEW</span>
      </div>
    </template>
    
    <!-- タイトルはデフォルトを使用 -->
    
    <!-- 価格スロットをカスタマイズ -->
    <template #price="{ product }">
      <p class="price">
        <span class="original-price">{{ product.price }}円</span>
        <span class="discount-price">{{ product.price * 0.8 }}円</span>
      </p>
    </template>
    
    <!-- アクションスロット -->
    <template #actions="{ product }">
      <button @click="addToCart(product)">カートに追加</button>
    </template>
  </ProductCard>
</template>
```

### defineSlots - スロットの型定義

TypeScriptを使う場合、`defineSlots`でスロットの型を定義できます。これにより、スロットのPropsの型が明確になり、エディタの補完が効きます。

```vue
<!-- ProductList.vue -->
<template>
  <ul>
    <li v-for="product in products" :key="product.id">
      <slot :product="product" :index="index"></slot>
    </li>
  </ul>
</template>

<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
}

interface Props {
  products: Product[]
}

defineProps<Props>()

// スロットの型を定義
defineSlots<{
  default(props: { product: Product; index: number }): any
}>()
</script>
```

複数のスロットがある場合：

```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <slot name="header" :title="title"></slot>
    <slot :content="content"></slot>
    <slot name="footer" :actions="actions"></slot>
  </div>
</template>

<script setup lang="ts">
interface Props {
  title: string
  content: string
  actions?: string[]
}

defineProps<Props>()

// 複数のスロットの型を定義
defineSlots<{
  header(props: { title: string }): any
  default(props: { content: string }): any
  footer(props: { actions?: string[] }): any
}>()
</script>
```

### 実践的な例：データテーブルコンポーネント

```vue
<!-- DataTable.vue -->
<template>
  <div class="data-table">
    <table>
      <thead>
        <tr>
          <th v-for="column in columns" :key="column.key">
            {{ column.label }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, rowIndex) in data" :key="row.id || rowIndex">
          <td v-for="column in columns" :key="column.key">
            <slot 
              name="cell" 
              :row="row" 
              :column="column" 
              :value="row[column.key]"
              :rowIndex="rowIndex"
            >
              <!-- デフォルト: 値をそのまま表示 -->
              {{ row[column.key] }}
            </slot>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
interface Column {
  key: string
  label: string
}

interface Props {
  columns: Column[]
  data: Record<string, any>[]
}

defineProps<Props>()

defineSlots<{
  cell(props: {
    row: Record<string, any>
    column: Column
    value: any
    rowIndex: number
  }): any
}>()
</script>
```

```vue
<!-- 使用例 -->
<template>
  <DataTable :columns="columns" :data="users">
    <template #cell="{ column, value, row }">
      <!-- ステータスカラムをカスタマイズ -->
      <span v-if="column.key === 'status'" :class="`status-${value}`">
        {{ value }}
      </span>
      
      <!-- メールカラムをカスタマイズ -->
      <a v-else-if="column.key === 'email'" :href="`mailto:${value}`">
        {{ value }}
      </a>
      
      <!-- その他はデフォルト表示 -->
      <span v-else>{{ value }}</span>
    </template>
  </DataTable>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import DataTable from './DataTable.vue'

const columns = [
  { key: 'name', label: '名前' },
  { key: 'email', label: 'メールアドレス' },
  { key: 'status', label: 'ステータス' }
]

const users = ref([
  { id: 1, name: '山田太郎', email: 'yamada@example.com', status: 'active' },
  { id: 2, name: '佐藤花子', email: 'sato@example.com', status: 'inactive' }
])
</script>
```

## 実践的な例

### 再利用可能なカードコンポーネント

```vue
<!-- ReusableCard.vue -->
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

```vue
<!-- 使用例 -->
<template>
  <ReusableCard variant="primary">
    <template #header>
      <h2>商品情報</h2>
    </template>
    
    <p>商品の説明文です</p>
    
    <template #footer>
      <button>購入する</button>
    </template>
  </ReusableCard>
</template>
```

## まとめ

- **computed**: リアクティブなデータから計算された値を返す（キャッシュされる）
- **スロット**: コンポーネントにコンテンツを渡す
- **名前付きスロット**: 複数のスロットを使う場合に名前を付ける（`#header`など）
- **スコープ付きスロット**: 子コンポーネントから親コンポーネントにデータを渡す（`#default="{ product }"`など）
- **defineSlots**: スロットの型を定義する（TypeScript）

## 参考資料

- [Vue.js - 算出プロパティ](https://ja.vuejs.org/guide/essentials/computed.html)
- [Vue.js - スロット](https://ja.vuejs.org/guide/components/slots.html)

