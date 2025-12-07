# イベントハンドリング

## 簡単な説明

**イベントハンドリング**は、ユーザーの操作（クリック、入力など）に応じて処理を実行する機能です。Vueでは、`@`記号（`v-on:`の省略形）を使ってイベントを処理します。

## 基本的な使い方

### @click - クリックイベント

```vue
<template>
  <div>
    <button @click="handleClick">クリック</button>
    
    <!-- インラインで処理を書くことも可能 -->
    <button @click="count++">カウント: {{ count }}</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

const handleClick = () => {
  console.log('ボタンがクリックされました')
  count.value++
}
</script>
```

### @input - 入力イベント

```vue
<template>
  <div>
    <input @input="handleInput" />
    <p>入力された値: {{ inputValue }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const inputValue = ref('')

const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  inputValue.value = target.value
}
</script>
```

## イベントオブジェクト

イベントハンドラーは、イベントオブジェクトを受け取れます。

```vue
<template>
  <button @click="handleClick">クリック</button>
</template>

<script setup lang="ts">
const handleClick = (event: MouseEvent) => {
  console.log('クリック位置:', event.clientX, event.clientY)
  console.log('クリックされた要素:', event.target)
}
</script>
```

## イベント修飾子

イベント修飾子を使うと、イベントの動作を変更できます。

### .prevent - デフォルト動作を防ぐ

```vue
<template>
  <!-- フォーム送信のデフォルト動作（ページリロード）を防ぐ -->
  <form @submit.prevent="handleSubmit">
    <input v-model="name" />
    <button type="submit">送信</button>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const name = ref('')

const handleSubmit = () => {
  console.log('送信:', name.value)
  // ページはリロードされない
}
</script>
```

### .stop - イベントの伝播を止める

```vue
<template>
  <div @click="handleOuterClick">
    外側
    <button @click.stop="handleInnerClick">内側</button>
  </div>
</template>

<script setup lang="ts">
const handleOuterClick = () => {
  console.log('外側がクリックされました')
}

const handleInnerClick = () => {
  console.log('内側がクリックされました')
  // .stopがあるので、外側のクリックイベントは発火しない
}
</script>
```

### .once - 一度だけ実行

```vue
<template>
  <button @click.once="handleClick">一度だけ実行</button>
</template>

<script setup lang="ts">
const handleClick = () => {
  console.log('これは一度だけ実行されます')
}
</script>
```

### 修飾子の組み合わせ

修飾子は複数組み合わせられます。

```vue
<template>
  <form @submit.prevent.once="handleSubmit">
    <!-- preventとonceを組み合わせ -->
  </form>
</template>
```

## キー修飾子

特定のキーが押された時だけ処理を実行できます。

```vue
<template>
  <div>
    <!-- Enterキーが押された時 -->
    <input @keyup.enter="handleEnter" />
    
    <!-- Escapeキーが押された時 -->
    <input @keyup.esc="handleEscape" />
    
    <!-- Ctrl + Enter が押された時 -->
    <input @keyup.ctrl.enter="handleCtrlEnter" />
  </div>
</template>

<script setup lang="ts">
const handleEnter = () => {
  console.log('Enterキーが押されました')
}

const handleEscape = () => {
  console.log('Escapeキーが押されました')
}

const handleCtrlEnter = () => {
  console.log('Ctrl + Enterが押されました')
}
</script>
```

## よく使うイベント

### @change - 値が変更された時

```vue
<template>
  <select @change="handleChange">
    <option value="apple">りんご</option>
    <option value="banana">バナナ</option>
    <option value="orange">オレンジ</option>
  </select>
</template>

<script setup lang="ts">
const handleChange = (event: Event) => {
  const target = event.target as HTMLSelectElement
  console.log('選択された値:', target.value)
}
</script>
```

### @focus / @blur - フォーカスイベント

```vue
<template>
  <input 
    @focus="handleFocus" 
    @blur="handleBlur" 
  />
</template>

<script setup lang="ts">
const handleFocus = () => {
  console.log('入力フィールドにフォーカスが当たりました')
}

const handleBlur = () => {
  console.log('入力フィールドからフォーカスが外れました')
}
</script>
```

### @mouseenter / @mouseleave - マウスイベント

```vue
<template>
  <div 
    @mouseenter="handleMouseEnter" 
    @mouseleave="handleMouseLeave"
  >
    マウスをホバー
  </div>
</template>

<script setup lang="ts">
const handleMouseEnter = () => {
  console.log('マウスが入りました')
}

const handleMouseLeave = () => {
  console.log('マウスが出ました')
}
</script>
```

## 実践的な例

### カウンターアプリ

```vue
<template>
  <div>
    <p>カウント: {{ count }}</p>
    <button @click="increment">増やす</button>
    <button @click="decrement">減らす</button>
    <button @click="reset">リセット</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++
}

const decrement = () => {
  count.value--
}

const reset = () => {
  count.value = 0
}
</script>
```

### フォームのバリデーション

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <input 
        v-model="email" 
        type="email" 
        @blur="validateEmail"
        placeholder="メールアドレス"
      />
      <span v-if="emailError" class="error">{{ emailError }}</span>
    </div>
    
    <button type="submit" :disabled="!!emailError">送信</button>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const email = ref('')
const emailError = ref('')

const validateEmail = () => {
  if (!email.value) {
    emailError.value = 'メールアドレスを入力してください'
  } else if (!email.value.includes('@')) {
    emailError.value = '有効なメールアドレスを入力してください'
  } else {
    emailError.value = ''
  }
}

const handleSubmit = () => {
  if (!emailError.value) {
    console.log('送信:', email.value)
  }
}
</script>

<style scoped>
.error {
  color: red;
  font-size: 0.875rem;
}
</style>
```

### モーダルの開閉

```vue
<template>
  <div>
    <button @click="openModal">モーダルを開く</button>
    
    <!-- モーダル -->
    <div v-if="isModalOpen" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <h2>モーダル</h2>
        <p>モーダルの内容</p>
        <button @click="closeModal">閉じる</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const isModalOpen = ref(false)

const openModal = () => {
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: white;
  padding: 24px;
  border-radius: 8px;
}
</style>
```

## まとめ

- **@click**: クリックイベント
- **@input**: 入力イベント
- **イベント修飾子**: `.prevent`、`.stop`、`.once`など
- **キー修飾子**: `.enter`、`.esc`など
- **イベントオブジェクト**: イベントの詳細情報を取得

## 参考資料

- [Vue.js - イベントハンドリング](https://ja.vuejs.org/guide/essentials/event-handling.html)

