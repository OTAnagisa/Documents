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

自分で作ったコンポーネントでも`v-model`を使えます。Vue 3.3以降では、`defineModel`を使うと簡単に実装できます。

#### 子コンポーネント（MyInput.vue）

```vue
<template>
  <input
    :value="model"
    @input="model = ($event.target as HTMLInputElement).value"
  />
</template>

<script setup lang="ts">
// defineModelを使うと、v-modelが自動的に機能します
const model = defineModel<string>({ required: true })
</script>
```

#### 親コンポーネント

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

### 名前付きv-model

1つのコンポーネントで複数の値を双方向バインドしたい場合、**名前付きv-model**を使います。

```vue
<!-- 子コンポーネント（MyForm.vue） -->
<template>
  <div>
    <input
      :value="firstNameModel"
      @input="firstNameModel = ($event.target as HTMLInputElement).value"
      placeholder="名前"
    />
    <input
      :value="lastNameModel"
      @input="lastNameModel = ($event.target as HTMLInputElement).value"
      placeholder="名字"
    />
  </div>
</template>

<script setup lang="ts">
// 名前付きv-modelを定義
const firstNameModel = defineModel<string>('firstName', { required: true })
const lastNameModel = defineModel<string>('lastName', { required: true })
</script>
```

```vue
<!-- 親コンポーネント -->
<template>
  <MyForm 
    v-model:firstName="first" 
    v-model:lastName="last" 
  />
  <p>フルネーム: {{ first }} {{ last }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import MyForm from './MyForm.vue'

const first = ref('')
const last = ref('')
</script>
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

## v-bind（:） - 単方向バインディング

`v-bind`（省略形は`:`）は、データを要素の属性やPropsに紐づけるためのディレクティブです。**単方向バインディング**で、データから要素への一方通行です。

### 基本的な使い方

```vue
<template>
  <div>
    <!-- v-bindの省略形 : を使う -->
    <img :src="imageUrl" :alt="imageAlt" />
    
    <!-- v-bind を省略せずに書く場合 -->
    <img v-bind:src="imageUrl" v-bind:alt="imageAlt" />
    
    <!-- クラスのバインド -->
    <div :class="{ active: isActive }">コンテンツ</div>
    
    <!-- Propsに変数を渡す -->
    <MyComponent :title="pageTitle" :count="itemCount" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import MyComponent from './MyComponent.vue'

const imageUrl = ref('/path/to/image.jpg')
const imageAlt = ref('説明文')
const isActive = ref(true)
const pageTitle = ref('ページタイトル')
const itemCount = ref(10)
</script>
```

### オブジェクトで複数の属性をバインド

```vue
<template>
  <div v-bind="attributes">
    コンテンツ
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const attributes = ref({
  id: 'my-div',
  class: 'container',
  'data-test': 'value'
})
</script>
```

## バインディングの種類

### 双方向バインディング vs 単方向バインディング

#### v-model（双方向バインディング）

`v-model`は**双方向バインディング**で、データと要素の値が同期します。

- **データ → 要素**: データが変わると要素の値も変わる
- **要素 → データ**: 要素の値が変わるとデータも変わる

```vue
<template>
  <input v-model="text" />
  <p>{{ text }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const text = ref('初期値')

// データを変更すると、入力フィールドの値も変わる
setTimeout(() => {
  text.value = '変更後の値'
}, 1000)
</script>
```

#### v-bind（単方向バインディング）

`v-bind`（`:`）は**単方向バインディング**で、データから要素への一方通行です。

- **データ → 要素**: データが変わると要素の値も変わる
- **要素 → データ**: 要素の値を変更してもデータは変わらない（フォーム要素の場合）

```vue
<template>
  <!-- 入力フィールドの値は表示されるが、入力してもデータは更新されない -->
  <input :value="text" />
  <p>{{ text }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const text = ref('初期値')

// データを変更すると、入力フィールドの値は変わる
setTimeout(() => {
  text.value = '変更後の値'
}, 1000)

// ただし、ユーザーが入力フィールドに文字を入力しても、textの値は変わらない
</script>
```

### 使い分け

- **v-model**: フォーム要素（input、textarea、selectなど）で、ユーザーの入力を受け取りたい場合
- **v-bind（:）**: Propsにデータを渡したり、属性の値を設定したりする場合

```vue
<template>
  <!-- v-model: ユーザーの入力を取得したい -->
  <input v-model="userInput" />
  
  <!-- v-bind: Propsや属性に値を設定する -->
  <MyComponent 
    :title="pageTitle" 
    :user-name="userName"
    :class="isActive ? 'active' : ''"
  />
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

