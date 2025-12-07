# AxiosとAPI通信

## 簡単な説明

**Axios**は、HTTPリクエストを送信するためのJavaScriptライブラリです。サーバーとデータをやり取りする際に使います。Vueアプリケーションから、APIサーバーにデータを取得したり、送信したりするために使用します。ブラウザの`fetch` APIよりも使いやすく、多くの機能が用意されています。

## Axiosとは

Axiosは、PromiseベースのHTTPクライアントライブラリです。サーバーとの通信（API呼び出し）を簡単に行えます。

### 主な特徴

- **Promiseベース**: 非同期処理が書きやすい
- **リクエスト/レスポンスのインターセプト**: リクエストやレスポンスを加工できる
- **自動的なJSON変換**: JSONデータを自動的に変換
- **エラーハンドリング**: エラー処理が簡単
- **ブラウザとNode.jsの両方で動作**

## Axiosのインストール

```bash
npm install axios
# または
yarn add axios
```

## 基本的な使い方

### GETリクエスト（データの取得）

```vue
<template>
  <div>
    <h1>ユーザー一覧</h1>
    <ul v-if="users.length">
      <li v-for="user in users" :key="user.id">
        {{ user.name }}
      </li>
    </ul>
    <p v-else>読み込み中...</p>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

interface User {
  id: number
  name: string
  email: string
}

const users = ref<User[]>([])

onMounted(async () => {
  try {
    const response = await axios.get<User[]>('/api/users')
    users.value = response.data
  } catch (error) {
    console.error('ユーザーの取得に失敗しました:', error)
  }
})
</script>
```

### POSTリクエスト（データの送信）

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <input v-model="form.name" type="text" placeholder="名前" />
    <input v-model="form.email" type="email" placeholder="メールアドレス" />
    <button type="submit">送信</button>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const form = ref({
  name: '',
  email: ''
})

const handleSubmit = async () => {
  try {
    const response = await axios.post('/api/users', form.value)
    console.log('送信成功:', response.data)
    // フォームをリセット
    form.value = { name: '', email: '' }
  } catch (error) {
    console.error('送信に失敗しました:', error)
  }
}
</script>
```

## 様々なHTTPメソッド

### GET - データの取得

```typescript
// 基本的なGETリクエスト
const response = await axios.get('/api/users')

// クエリパラメータ付き
const response = await axios.get('/api/users', {
  params: {
    page: 1,
    limit: 10
  }
})
// URL: /api/users?page=1&limit=10
```

### POST - データの作成

```typescript
const response = await axios.post('/api/users', {
  name: '山田太郎',
  email: 'yamada@example.com'
})
```

### PUT - データの更新（全体）

```typescript
const response = await axios.put('/api/users/1', {
  name: '山田花子',
  email: 'yamada-hanako@example.com'
})
```

### PATCH - データの更新（一部）

```typescript
const response = await axios.patch('/api/users/1', {
  name: '山田花子'
})
```

### DELETE - データの削除

```typescript
const response = await axios.delete('/api/users/1')
```

## レスポンスの処理

### レスポンスオブジェクトの構造

```typescript
const response = await axios.get('/api/users')

// response.data: サーバーから返されたデータ
console.log(response.data)

// response.status: HTTPステータスコード（200, 404など）
console.log(response.status)

// response.headers: レスポンスヘッダー
console.log(response.headers)

// response.config: リクエストの設定
console.log(response.config)
```

### 型安全なレスポンス

TypeScriptを使う場合、レスポンスの型を指定できます。

```typescript
interface User {
  id: number
  name: string
  email: string
}

// レスポンスの型を指定
const response = await axios.get<User[]>('/api/users')
const users: User[] = response.data
```

## エラーハンドリング

### try-catchでのエラーハンドリング

```typescript
try {
  const response = await axios.get('/api/users')
  console.log(response.data)
} catch (error) {
  if (axios.isAxiosError(error)) {
    // Axiosのエラー
    console.error('エラーメッセージ:', error.message)
    console.error('ステータスコード:', error.response?.status)
    console.error('エラーデータ:', error.response?.data)
  } else {
    // その他のエラー
    console.error('予期しないエラー:', error)
  }
}
```

### エラーオブジェクトの構造

```typescript
catch (error) {
  if (axios.isAxiosError(error)) {
    // error.response: サーバーからのレスポンス（エラーの場合）
    if (error.response) {
      console.log(error.response.status) // ステータスコード
      console.log(error.response.data)   // エラーデータ
    }
    
    // error.request: リクエストは送信されたが、レスポンスがなかった
    else if (error.request) {
      console.log('サーバーからの応答がありません')
    }
    
    // error.message: エラーメッセージ
    else {
      console.log('エラー:', error.message)
    }
  }
}
```

## 実践的な例

### ユーザー一覧の取得と表示

```vue
<template>
  <div>
    <h1>ユーザー一覧</h1>
    
    <div v-if="loading">読み込み中...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <ul>
        <li v-for="user in users" :key="user.id">
          {{ user.name }} ({{ user.email }})
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

interface User {
  id: number
  name: string
  email: string
}

const users = ref<User[]>([])
const loading = ref(true)
const error = ref('')

const fetchUsers = async () => {
  try {
    loading.value = true
    error.value = ''
    const response = await axios.get<User[]>('/api/users')
    users.value = response.data
  } catch (err) {
    if (axios.isAxiosError(err)) {
      error.value = 'ユーザーの取得に失敗しました'
    } else {
      error.value = '予期しないエラーが発生しました'
    }
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchUsers()
})
</script>
```

### フォーム送信とエラーハンドリング

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label>名前:</label>
      <input v-model="form.name" type="text" required />
      <span v-if="errors.name" class="error">{{ errors.name }}</span>
    </div>
    
    <div>
      <label>メールアドレス:</label>
      <input v-model="form.email" type="email" required />
      <span v-if="errors.email" class="error">{{ errors.email }}</span>
    </div>
    
    <button type="submit" :disabled="submitting">
      {{ submitting ? '送信中...' : '送信' }}
    </button>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const form = ref({
  name: '',
  email: ''
})

const errors = ref<Record<string, string>>({})
const submitting = ref(false)

const handleSubmit = async () => {
  errors.value = {}
  submitting.value = true
  
  try {
    const response = await axios.post('/api/users', form.value)
    console.log('送信成功:', response.data)
    
    // フォームをリセット
    form.value = { name: '', email: '' }
    
    // 成功メッセージを表示（実装は省略）
    alert('送信に成功しました')
  } catch (err) {
    if (axios.isAxiosError(err) && err.response) {
      // バリデーションエラーなど
      if (err.response.status === 422) {
        errors.value = err.response.data.errors || {}
      } else {
        alert('送信に失敗しました')
      }
    } else {
      alert('ネットワークエラーが発生しました')
    }
  } finally {
    submitting.value = false
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

### Axiosインスタンスの作成

同じ設定を複数回使う場合、Axiosインスタンスを作成すると便利です。

```typescript
// api/client.ts
import axios from 'axios'

const apiClient = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
})

// リクエストインターセプター
apiClient.interceptors.request.use(
  (config) => {
    // トークンを追加するなど
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

// レスポンスインターセプター
apiClient.interceptors.response.use(
  (response) => {
    return response
  },
  (error) => {
    // 401エラーの場合、ログインページにリダイレクト
    if (error.response?.status === 401) {
      // ログインページにリダイレクト
    }
    return Promise.reject(error)
  }
)

export default apiClient
```

```vue
<!-- 使用例 -->
<script setup lang="ts">
import apiClient from '@/api/client'

const fetchUsers = async () => {
  // baseURLが自動的に追加される
  const response = await apiClient.get('/users')
  return response.data
}
</script>
```

## まとめ

- **Axios**: HTTPリクエストを送信するライブラリ
- **GET**: データの取得
- **POST**: データの作成
- **PUT/PATCH**: データの更新
- **DELETE**: データの削除
- **エラーハンドリング**: try-catchでエラーを処理
- **Axiosインスタンス**: 共通設定をまとめて管理

## 参考資料

- [Axios 公式ドキュメント（日本語）](https://axios-http.com/ja/docs/intro)
- [Axios - リクエスト設定](https://axios-http.com/ja/docs/req_config)
- [Axios - レスポンススキーマ](https://axios-http.com/ja/docs/res_schema)

