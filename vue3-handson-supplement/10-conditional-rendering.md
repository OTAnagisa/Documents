# 条件付きレンダリングとリストレンダリング

## 簡単な説明

**条件付きレンダリング**は、条件に応じて要素を表示したり非表示にしたりする機能です。**リストレンダリング**は、配列のデータを繰り返し表示する機能です。これらを使うことで、動的なUIを作成できます。

## v-if - 条件に応じて表示/非表示

`v-if`は、条件が`true`の時だけ要素を表示します。条件が`false`の時は、DOMから要素が削除されます。

### 基本的な使い方

```vue
<template>
  <div>
    <!-- 条件がtrueの時だけ表示 -->
    <p v-if="isVisible">このメッセージは表示されます</p>
    
    <!-- 条件がfalseの時は表示されない -->
    <p v-if="!isVisible">このメッセージは表示されません</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const isVisible = ref(true)
</script>
```

### v-else - 条件がfalseの時の表示

`v-else`は、直前の`v-if`の条件が`false`の時に表示されます。

```vue
<template>
  <div>
    <p v-if="isLoggedIn">ログイン中です</p>
    <p v-else>ログインしてください</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const isLoggedIn = ref(false)
</script>
```

### v-else-if - 複数の条件

`v-else-if`で、複数の条件をチェックできます。

```vue
<template>
  <div>
    <p v-if="score >= 90">優秀</p>
    <p v-else-if="score >= 70">良好</p>
    <p v-else-if="score >= 50">普通</p>
    <p v-else>要努力</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const score = ref(85)
</script>
```

## v-show - 表示/非表示の切り替え

`v-show`は、`v-if`と似ていますが、要素をDOMから削除せず、CSSの`display`プロパティで表示/非表示を切り替えます。

### v-if vs v-show

| 特徴 | v-if | v-show |
|------|------|--------|
| DOM | 条件がfalseの時、要素を削除 | 要素は残る（display: none） |
| 切り替えコスト | 高い（要素の追加/削除） | 低い（CSSの切り替えのみ） |
| 初期レンダリング | 条件がfalseの時、レンダリングされない | 常にレンダリングされる |
| 使い分け | 切り替えが少ない場合 | 頻繁に切り替える場合 |

```vue
<template>
  <div>
    <!-- v-if: 条件がfalseの時、DOMから削除される -->
    <div v-if="showMessage">v-ifのメッセージ</div>
    
    <!-- v-show: 条件がfalseの時、display: noneになる -->
    <div v-show="showMessage">v-showのメッセージ</div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const showMessage = ref(true)
</script>
```

## v-for - リストレンダリング

`v-for`は、配列やオブジェクトのデータを繰り返し表示します。

### 配列の繰り返し

```vue
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Item {
  id: number
  name: string
}

const items = ref<Item[]>([
  { id: 1, name: 'りんご' },
  { id: 2, name: 'バナナ' },
  { id: 3, name: 'オレンジ' }
])
</script>
```

### インデックスを取得する

```vue
<template>
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      {{ index + 1 }}. {{ item.name }}
    </li>
  </ul>
</template>
```

### オブジェクトの繰り返し

```vue
<template>
  <ul>
    <li v-for="(value, key) in user" :key="key">
      {{ key }}: {{ value }}
    </li>
  </ul>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const user = ref({
  name: '山田太郎',
  age: 25,
  email: 'yamada@example.com'
})
</script>
```

### :key の重要性

`v-for`を使う時は、必ず`:key`を指定します。`:key`は、各要素を一意に識別するためのものです。

```vue
<template>
  <!-- ✅ 正しい: 一意のkeyを指定 -->
  <div v-for="item in items" :key="item.id">
    {{ item.name }}
  </div>
  
  <!-- ❌ 避けるべき: インデックスをkeyに使う（データが変更されると問題が起きる可能性） -->
  <div v-for="(item, index) in items" :key="index">
    {{ item.name }}
  </div>
</template>
```

## 実践的な例

### 商品一覧の表示

```vue
<template>
  <div>
    <h1>商品一覧</h1>
    
    <!-- 商品がある場合 -->
    <div v-if="products.length > 0">
      <div v-for="product in products" :key="product.id" class="product-card">
        <h3>{{ product.name }}</h3>
        <p>{{ product.price }}円</p>
      </div>
    </div>
    
    <!-- 商品がない場合 -->
    <p v-else>商品がありません</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Product {
  id: number
  name: string
  price: number
}

const products = ref<Product[]>([
  { id: 1, name: 'ノートパソコン', price: 50000 },
  { id: 2, name: 'マウス', price: 2000 }
])
</script>
```

### ローディング状態の表示

```vue
<template>
  <div>
    <!-- ローディング中 -->
    <div v-if="loading">読み込み中...</div>
    
    <!-- エラー時 -->
    <div v-else-if="error">{{ error }}</div>
    
    <!-- データ表示 -->
    <div v-else>
      <ul>
        <li v-for="item in items" :key="item.id">
          {{ item.name }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const loading = ref(true)
const error = ref('')
const items = ref([])
</script>
```

### フィルタリングとソート

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

// フィルタリングされたアイテム
const filteredItems = computed(() => {
  if (!searchQuery.value) {
    return items.value
  }
  return items.value.filter(item =>
    item.name.includes(searchQuery.value)
  )
})
</script>
```

## まとめ

- **v-if**: 条件に応じて要素を表示/非表示（DOMから削除）
- **v-show**: CSSで表示/非表示を切り替え（要素は残る）
- **v-for**: 配列やオブジェクトを繰り返し表示
- **:key**: v-forで必ず指定する（一意の識別子）
- **v-else / v-else-if**: 複数の条件分岐

## 参考資料

- [Vue.js - 条件付きレンダリング](https://ja.vuejs.org/guide/essentials/conditional.html)
- [Vue.js - リストレンダリング](https://ja.vuejs.org/guide/essentials/list.html)

