# Vueのリアクティビティ

## 簡単な説明

**リアクティビティ**とは、データが変更されると、そのデータを使っている画面の表示が自動的に更新される仕組みです。Vueでは、`ref()`や`reactive()`を使ってリアクティブなデータを作成します。これにより、データを変更するだけで、画面が自動的に更新されます。

## リアクティビティとは

従来のJavaScriptでは、データを変更しても画面は自動的に更新されません。手動でDOMを操作する必要がありました。

```javascript
// 従来のJavaScript
let count = 0
document.getElementById('count').textContent = count // 手動で更新

count = 1
document.getElementById('count').textContent = count // また手動で更新
```

Vueのリアクティビティでは、データを変更するだけで画面が自動的に更新されます。

```vue
<template>
  <div>{{ count }}</div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0) // リアクティブなデータ

// countを変更すると、画面が自動的に更新される
count.value = 1
</script>
```

## ref() の使い方

`ref()`は、単一の値をリアクティブにするための関数です。数値、文字列、真偽値など、あらゆる型の値に使えます。

### 基本的な使い方

```vue
<template>
  <div>
    <p>カウント: {{ count }}</p>
    <button @click="increment">増やす</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// ref()でリアクティブなデータを作成
const count = ref(0)

// 値を変更するときは .value を使う
const increment = () => {
  count.value = count.value + 1
}
</script>
```

### 重要なポイント

1. **作成時**: `ref(0)`のように初期値を渡す
2. **読み取り時**: テンプレート内では自動的にアンラップされるので、`{{ count }}`と書ける
3. **変更時**: スクリプト内では`.value`を使って`count.value = 1`のように変更する

### テンプレート内での自動アンラップ

テンプレート内では、`ref`が自動的にアンラップ（展開）されるため、`.value`を書く必要はありません。

```vue
<template>
  <!-- ✅ 正しい: 自動的にアンラップされる -->
  <div>{{ count }}</div>
  
  <!-- ❌ 間違い: .valueは不要 -->
  <div>{{ count.value }}</div>
</template>
```

### スクリプト内での.valueの使用

スクリプト内では、値を読み取る時も変更する時も`.value`を使います。

```typescript
import { ref } from 'vue'

const count = ref(0)

// 読み取り
console.log(count.value) // 0

// 変更
count.value = 10
console.log(count.value) // 10

// 計算
count.value = count.value + 5
```

## reactive() の使い方

`reactive()`は、オブジェクト全体をリアクティブにするための関数です。複数のプロパティを持つオブジェクトに使います。

### 基本的な使い方

```vue
<template>
  <div>
    <p>名前: {{ user.name }}</p>
    <p>年齢: {{ user.age }}</p>
    <button @click="updateUser">更新</button>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue'

// reactive()でオブジェクトをリアクティブにする
const user = reactive({
  name: '山田太郎',
  age: 25
})

// .valueは不要で、直接プロパティを変更できる
const updateUser = () => {
  user.name = '佐藤花子'
  user.age = 30
}
</script>
```

### ref() vs reactive()

| 特徴 | ref() | reactive() |
|------|-------|------------|
| 用途 | 単一の値 | オブジェクト全体 |
| 値のアクセス | `.value`が必要 | 直接アクセス可能 |
| 再代入 | 可能 | 推奨されない |
| 推奨 | ✅ 主要なAPI | オブジェクトの場合のみ |

**推奨**: 基本的には`ref()`を使うことを推奨します。オブジェクトの場合も、`ref({ ... })`として使えます。

```typescript
// オブジェクトもref()で扱える
const user = ref({
  name: '山田太郎',
  age: 25
})

// アクセス時は .value を使う
user.value.name = '佐藤花子'
```

## リアクティブな状態の更新

### 配列の操作

```vue
<template>
  <ul>
    <li v-for="item in items" :key="item">{{ item }}</li>
  </ul>
  <button @click="addItem">追加</button>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const items = ref<string[]>(['りんご', 'バナナ'])

const addItem = () => {
  // 配列に要素を追加
  items.value.push('オレンジ')
  
  // または新しい配列を作成
  items.value = [...items.value, 'オレンジ']
}
</script>
```

### オブジェクトのプロパティ追加

```typescript
import { ref } from 'vue'

const user = ref({
  name: '山田太郎',
  age: 25
})

// 新しいプロパティを追加
user.value.email = 'yamada@example.com'
```

## リアクティビティの注意点

### 1. 直接代入は避ける（reactiveの場合）

`reactive()`で作成したオブジェクトは、全体を再代入しないようにします。

```typescript
// ❌ 避けるべき
const state = reactive({ count: 0 })
state = { count: 1 } // リアクティビティが失われる

// ✅ 正しい
state.count = 1
```

### 2. 分割代入に注意

リアクティブなオブジェクトを分割代入すると、リアクティビティが失われます。

```typescript
import { reactive } from 'vue'

const state = reactive({ count: 0, name: 'Vue' })

// ❌ リアクティビティが失われる
const { count, name } = state

// ✅ 正しい: プロパティに直接アクセス
console.log(state.count)
```

## watch - データの変更を監視する

`watch`を使うと、リアクティブなデータの変更を監視して、変更時に処理を実行できます。

### 基本的な使い方

```vue
<template>
  <div>
    <input v-model="message" placeholder="メッセージを入力" />
    <p>入力された文字数: {{ messageLength }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

const message = ref('')
const messageLength = ref(0)

// messageが変更されるたびに実行される
watch(message, (newValue, oldValue) => {
  messageLength.value = newValue.length
  console.log(`メッセージが "${oldValue}" から "${newValue}" に変更されました`)
})
</script>
```

### 複数の値を監視する

```typescript
import { ref, watch } from 'vue'

const firstName = ref('')
const lastName = ref('')
const fullName = ref('')

// 複数の値を監視
watch([firstName, lastName], ([newFirst, newLast]) => {
  fullName.value = `${newFirst} ${newLast}`.trim()
})
```

### 即座に実行する（immediate）

`immediate: true`を指定すると、最初にも実行されます。

```typescript
watch(message, (newValue) => {
  console.log(newValue)
}, { immediate: true })
```

## ライフサイクルフック

ライフサイクルフックは、コンポーネントの特定のタイミングで処理を実行するための関数です。

### 主なライフサイクルフック

| フック | タイミング | 用途 |
|--------|-----------|------|
| `onMounted` | コンポーネントがDOMにマウントされた後 | DOM操作、API呼び出し |
| `onUpdated` | コンポーネントが更新された後 | 更新後の処理 |
| `onUnmounted` | コンポーネントがアンマウントされる前 | クリーンアップ |
| `onBeforeMount` | コンポーネントがマウントされる前 | マウント前の処理 |
| `onBeforeUpdate` | コンポーネントが更新される前 | 更新前の処理 |
| `onBeforeUnmount` | コンポーネントがアンマウントされる前 | クリーンアップ |

### onMounted - マウント時に実行

```vue
<template>
  <div>
    <h1>ユーザー一覧</h1>
    <ul v-if="users.length">
      <li v-for="user in users" :key="user.id">{{ user.name }}</li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const users = ref([])

// コンポーネントが表示された後に実行される
onMounted(async () => {
  // APIからデータを取得
  const response = await fetch('/api/users')
  users.value = await response.json()
})
</script>
```

### onUnmounted - アンマウント時にクリーンアップ

```vue
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

let intervalId: number | null = null

onMounted(() => {
  // タイマーを開始
  intervalId = window.setInterval(() => {
    console.log('タイマー実行中')
  }, 1000)
})

// コンポーネントが削除される前に実行される
onUnmounted(() => {
  // タイマーをクリーンアップ
  if (intervalId !== null) {
    clearInterval(intervalId)
  }
})
</script>
```

### 複数のフックを組み合わせる

```vue
<script setup lang="ts">
import { ref, onMounted, onUpdated, onUnmounted } from 'vue'

const count = ref(0)

onMounted(() => {
  console.log('コンポーネントがマウントされました')
})

onUpdated(() => {
  console.log(`カウントが更新されました: ${count.value}`)
})

onUnmounted(() => {
  console.log('コンポーネントがアンマウントされました')
})
</script>
```

## まとめ

- **リアクティビティ**: データの変更が自動的に画面に反映される仕組み
- **ref()**: 単一の値をリアクティブにする（推奨）
- **reactive()**: オブジェクト全体をリアクティブにする
- **テンプレート内**: 自動的にアンラップされるので`.value`不要
- **スクリプト内**: `ref()`を使う場合は`.value`が必要
- **watch**: データの変更を監視して処理を実行
- **ライフサイクルフック**: コンポーネントの特定のタイミングで処理を実行

## 参考資料

- [Vue.js - リアクティビティーの基礎](https://ja.vuejs.org/guide/essentials/reactivity-fundamentals)
- [Vue.js - ウォッチャー](https://ja.vuejs.org/guide/essentials/watchers.html)
- [Vue.js - ライフサイクルフック](https://ja.vuejs.org/guide/essentials/lifecycle.html)

