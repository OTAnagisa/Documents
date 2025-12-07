# Vue Routerについて

## 簡単な説明

**Vue Router**は、Vue.jsアプリケーションでページ遷移（ルーティング）を実現するためのライブラリです。通常のWebサイトのように、URLが変わると表示する内容が変わる仕組みを、SPA（Single Page Application）で実現できます。ブラウザの戻る/進むボタンも正しく動作します。

## ルーティングとは

従来のWebサイトでは、ページごとに異なるHTMLファイルがあり、リンクをクリックすると新しいページが読み込まれます。

```
従来のWebサイト:
/about.html → 新しいページを読み込む
/contact.html → 新しいページを読み込む
```

SPAでは、1つのHTMLファイルで全てのページを管理します。Vue Routerを使うと、URLに応じて表示するコンポーネントを切り替えることができます。

```
SPA（Vue Router使用）:
/about → 同じHTMLファイル、コンポーネントを切り替え
/contact → 同じHTMLファイル、コンポーネントを切り替え
```

## Vue Routerの基本設定

### ルーターの作成

```typescript
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'
import About from '../views/About.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

### アプリケーションでの使用

```typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

```vue
<!-- App.vue -->
<template>
  <div id="app">
    <nav>
      <router-link to="/">ホーム</router-link>
      <router-link to="/about">について</router-link>
    </nav>
    
    <!-- ルートに応じてコンポーネントが表示される -->
    <router-view />
  </div>
</template>
```

## 基本的な使い方

### router-link - リンクの作成

`<router-link>`は、ページ遷移用のリンクコンポーネントです。

```vue
<template>
  <div>
    <!-- 基本的な使い方 -->
    <router-link to="/">ホーム</router-link>
    <router-link to="/about">について</router-link>
    
    <!-- nameを使った指定 -->
    <router-link :to="{ name: 'Home' }">ホーム</router-link>
    
    <!-- アクティブなリンクにクラスを追加 -->
    <router-link to="/about" active-class="active">について</router-link>
  </div>
</template>
```

### router-view - コンポーネントの表示

`<router-view>`は、現在のルートに応じたコンポーネントを表示する場所です。

```vue
<template>
  <div>
    <Header />
    <router-view /> <!-- ここにルートに応じたコンポーネントが表示される -->
    <Footer />
  </div>
</template>
```

### プログラムでの遷移

JavaScriptコードからページ遷移を行う場合、`useRouter`を使います。

```vue
<template>
  <button @click="goToAbout">Aboutページへ</button>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const goToAbout = () => {
  router.push('/about')
  // または
  router.push({ name: 'About' })
}
</script>
```

## ルートパラメータ

URLに動的な値を含めることができます。

### パラメータの定義

```typescript
// router/index.ts
const routes = [
  {
    path: '/user/:id',
    name: 'User',
    component: User
  }
]
```

### パラメータの取得

```vue
<!-- User.vue -->
<template>
  <div>
    <h1>ユーザーID: {{ $route.params.id }}</h1>
  </div>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
const userId = route.params.id
</script>
```

### パラメータ付きリンク

```vue
<template>
  <div>
    <!-- パスで指定 -->
    <router-link to="/user/123">ユーザー123</router-link>
    
    <!-- オブジェクトで指定 -->
    <router-link :to="{ name: 'User', params: { id: 123 } }">
      ユーザー123
    </router-link>
  </div>
</template>
```

## クエリパラメータ

URLの`?`以降のパラメータ（クエリパラメータ）も取得できます。

```
例: /search?q=vue&page=1
```

```vue
<template>
  <div>
    <h1>検索結果</h1>
    <p>検索キーワード: {{ $route.query.q }}</p>
    <p>ページ: {{ $route.query.page }}</p>
  </div>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
const query = route.query.q
const page = route.query.page
</script>
```

```vue
<!-- クエリパラメータ付きリンク -->
<template>
  <router-link :to="{ path: '/search', query: { q: 'vue', page: 1 } }">
    検索
  </router-link>
</template>
```

## ネストされたルート

ルートの中にさらにルートをネストできます。

```typescript
// router/index.ts
const routes = [
  {
    path: '/user/:id',
    component: User,
    children: [
      {
        path: 'profile',
        component: UserProfile
      },
      {
        path: 'posts',
        component: UserPosts
      }
    ]
  }
]
```

```vue
<!-- User.vue -->
<template>
  <div>
    <h1>ユーザー: {{ $route.params.id }}</h1>
    <nav>
      <router-link :to="`/user/${$route.params.id}/profile`">プロフィール</router-link>
      <router-link :to="`/user/${$route.params.id}/posts`">投稿</router-link>
    </nav>
    
    <!-- 子ルートのコンポーネントが表示される -->
    <router-view />
  </div>
</template>
```

## ナビゲーションガード

特定の条件でページ遷移を制御できます。

### ルートガード

```typescript
// router/index.ts
const routes = [
  {
    path: '/admin',
    component: Admin,
    beforeEnter: (to, from, next) => {
      // 認証チェックなど
      if (isAuthenticated()) {
        next()
      } else {
        next('/login')
      }
    }
  }
]
```

### グローバルガード

```typescript
// router/index.ts
router.beforeEach((to, from, next) => {
  // 全てのルート遷移前に実行
  console.log(`遷移: ${from.path} → ${to.path}`)
  next()
})
```

## 実践的な例

### 基本的なアプリケーション構造

```vue
<!-- App.vue -->
<template>
  <div id="app">
    <header>
      <nav>
        <router-link to="/">ホーム</router-link>
        <router-link to="/products">商品一覧</router-link>
        <router-link to="/about">について</router-link>
      </nav>
    </header>
    
    <main>
      <router-view />
    </main>
    
    <footer>
      <p>&copy; 2024 My App</p>
    </footer>
  </div>
</template>
```

### 商品詳細ページの例

```typescript
// router/index.ts
const routes = [
  {
    path: '/products',
    name: 'Products',
    component: ProductList
  },
  {
    path: '/products/:id',
    name: 'ProductDetail',
    component: ProductDetail
  }
]
```

```vue
<!-- ProductList.vue -->
<template>
  <div>
    <h1>商品一覧</h1>
    <div v-for="product in products" :key="product.id">
      <h3>{{ product.name }}</h3>
      <router-link :to="`/products/${product.id}`">詳細を見る</router-link>
    </div>
  </div>
</template>
```

```vue
<!-- ProductDetail.vue -->
<template>
  <div>
    <h1>{{ product.name }}</h1>
    <p>{{ product.description }}</p>
    <p>価格: {{ product.price }}円</p>
    <router-link to="/products">一覧に戻る</router-link>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const product = ref(null)

onMounted(async () => {
  const productId = route.params.id
  // APIから商品データを取得
  const response = await fetch(`/api/products/${productId}`)
  product.value = await response.json()
})
</script>
```

## まとめ

- **Vue Router**: SPAでページ遷移を実現するライブラリ
- **router-link**: ページ遷移用のリンクコンポーネント
- **router-view**: ルートに応じたコンポーネントを表示
- **ルートパラメータ**: URLに動的な値を含める
- **クエリパラメータ**: `?`以降のパラメータを取得
- **ナビゲーションガード**: ページ遷移を制御

## 参考資料

- [Vue Router 公式ドキュメント（日本語）](https://router.vuejs.org/ja/)
- [Vue Router - はじめに](https://router.vuejs.org/ja/guide/)

