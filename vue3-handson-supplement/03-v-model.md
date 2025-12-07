# v-modelとフォーム入力

## 簡単な説明

**v-model**は、フォーム要素（入力フィールド、チェックボックスなど）とVueのデータを双方向にバインドするためのディレクティブです。ユーザーが入力すると自動的にデータが更新され、データを変更すると入力フィールドの値も自動的に更新されます。

## v-modelの基本

### 基本的な使い方

```vue
<template>
  <div>
    <input v-model="message" placeholder="メッセージを入力" />
    <p>入力された値: {{ message }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const message = ref('')
</script>
```

この例では：
- ユーザーが入力フィールドに文字を入力すると、`message`が自動的に更新される
- `message`の値を変更すると、入力フィールドの値も自動的に更新される

## 様々なフォーム要素での使用

### テキスト入力（input type="text"）

```vue
<template>
  <input v-model="text" type="text" />
  <p>{{ text }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const text = ref('')
</script>
```

### テキストエリア（textarea）

```vue
<template>
  <textarea v-model="description"></textarea>
  <p>{{ description }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const description = ref('')
</script>
```

### チェックボックス（checkbox）

```vue
<template>
  <div>
    <input type="checkbox" v-model="checked" id="agree" />
    <label for="agree">同意する</label>
    <p>チェック状態: {{ checked }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const checked = ref(false)
</script>
```

### ラジオボタン（radio）

```vue
<template>
  <div>
    <input type="radio" v-model="selected" value="option1" id="opt1" />
    <label for="opt1">オプション1</label>
    
    <input type="radio" v-model="selected" value="option2" id="opt2" />
    <label for="opt2">オプション2</label>
    
    <p>選択された値: {{ selected }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const selected = ref('option1')
</script>
```

### セレクトボックス（select）

```vue
<template>
  <div>
    <select v-model="selectedOption">
      <option value="">選択してください</option>
      <option value="apple">りんご</option>
      <option value="banana">バナナ</option>
      <option value="orange">オレンジ</option>
    </select>
    <p>選択された値: {{ selectedOption }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const selectedOption = ref('')
</script>
```

### 複数のチェックボックス

```vue
<template>
  <div>
    <input type="checkbox" v-model="fruits" value="apple" id="apple" />
    <label for="apple">りんご</label>
    
    <input type="checkbox" v-model="fruits" value="banana" id="banana" />
    <label for="banana">バナナ</label>
    
    <input type="checkbox" v-model="fruits" value="orange" id="orange" />
    <label for="orange">オレンジ</label>
    
    <p>選択された果物: {{ fruits }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// 配列で管理
const fruits = ref<string[]>([])
</script>
```

## v-modelの修飾子

### .trim - 前後の空白を削除

```vue
<template>
  <input v-model.trim="text" />
  <p>「{{ text }}」</p>
</template>
```

### .number - 数値に変換

```vue
<template>
  <input v-model.number="age" type="number" />
  <p>年齢: {{ age }} (型: {{ typeof age }})</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const age = ref<number | string>('')
</script>
```

### .lazy - 入力イベントではなくchangeイベントで更新

```vue
<template>
  <!-- 通常: 入力のたびに更新 -->
  <input v-model="text1" />
  
  <!-- lazy: Enterキーやフォーカスが外れたときに更新 -->
  <input v-model.lazy="text2" />
</template>
```

## カスタムコンポーネントでのv-model

自分で作ったコンポーネントでも`v-model`を使えます。

### 子コンポーネント（MyInput.vue）

```vue
<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', ($event.target as HTMLInputElement).value)"
  />
</template>

<script setup lang="ts">
interface Props {
  modelValue: string
}

defineProps<Props>()

defineEmits<{
  'update:modelValue': [value: string]
}>()
</script>
```

### 親コンポーネント

```vue
<template>
  <MyInput v-model="message" />
  <p>{{ message }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import MyInput from './MyInput.vue'

const message = ref('')
</script>
```

### 複数のv-model（Vue 3.3+）

Vue 3.3以降では、複数の`v-model`を使えます。

```vue
<!-- 子コンポーネント -->
<template>
  <input
    :value="firstName"
    @input="$emit('update:firstName', ($event.target as HTMLInputElement).value)"
  />
  <input
    :value="lastName"
    @input="$emit('update:lastName', ($event.target as HTMLInputElement).value)"
  />
</template>

<script setup lang="ts">
interface Props {
  firstName: string
  lastName: string
}

defineProps<Props>()

defineEmits<{
  'update:firstName': [value: string]
  'update:lastName': [value: string]
}>()
</script>
```

```vue
<!-- 親コンポーネント -->
<template>
  <MyForm v-model:firstName="first" v-model:lastName="last" />
</template>
```

## フォーム送信の例

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label>名前:</label>
      <input v-model.trim="form.name" type="text" required />
    </div>
    
    <div>
      <label>メールアドレス:</label>
      <input v-model.trim="form.email" type="email" required />
    </div>
    
    <div>
      <label>メッセージ:</label>
      <textarea v-model.trim="form.message"></textarea>
    </div>
    
    <button type="submit">送信</button>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const form = ref({
  name: '',
  email: '',
  message: ''
})

const handleSubmit = () => {
  console.log('送信されたデータ:', form.value)
  // APIに送信する処理など
}
</script>
```

## v-modelとv-bindの違い

- **v-model**: 双方向バインディング（データ ↔ フォーム要素）
- **v-bind（:）**: 単方向バインディング（データ → フォーム要素）

```vue
<template>
  <!-- v-model: 双方向 -->
  <input v-model="text" />
  
  <!-- v-bind: 単方向（値の変更は反映されない） -->
  <input :value="text" />
</template>
```

## まとめ

- **v-model**: フォーム要素とデータを双方向にバインド
- **様々な要素**: input、textarea、checkbox、radio、selectで使用可能
- **修飾子**: `.trim`、`.number`、`.lazy`などが使える
- **カスタムコンポーネント**: 自分で作ったコンポーネントでも`v-model`を使える
- **フォーム送信**: `@submit.prevent`でフォーム送信を処理

## 参考資料

- [Vue.js - フォーム入力バインディング](https://ja.vuejs.org/guide/essentials/forms.html)
- [Vue.js - コンポーネントの v-model](https://ja.vuejs.org/guide/components/v-model.html)

