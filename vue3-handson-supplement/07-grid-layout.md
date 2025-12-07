# グリッドレイアウト（Vuetify）

## 簡単な説明

**グリッドレイアウト**は、画面を縦横に分割して要素を配置するレイアウトシステムです。Vuetifyでは、12列のグリッドシステムを使って、レスポンシブ（画面サイズに応じて変わる）なレイアウトを作成できます。画面サイズが変わっても、適切にレイアウトが調整されます。

## グリッドシステムとは

グリッドシステムは、画面を12列に分割して、要素の幅を指定するシステムです。

```
画面を12列に分割:
| 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
```

要素の幅を「12列のうち何列分使うか」で指定します。

- **12列**: 画面全体の幅
- **6列**: 画面の半分の幅
- **4列**: 画面の3分の1の幅
- **3列**: 画面の4分の1の幅

## グリッドコンポーネント

このハンズオンでは、以下のカスタムコンポーネントを使います：

- **BaseGrid**: コンテナ（最大幅を制限）
- **BaseRow**: 行（横方向のグループ）
- **BaseCol**: 列（実際の要素を配置）

### 基本的な構造

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <BaseCol cols="12" md="6">
        <!-- コンテンツ1 -->
      </BaseCol>
      <BaseCol cols="12" md="6">
        <!-- コンテンツ2 -->
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>
```

### 基本的な例

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <!-- 画面全体の幅（12列） -->
      <BaseCol cols="12">
        <div class="box">全幅</div>
      </BaseCol>
    </BaseRow>
    
    <BaseRow>
      <!-- 画面の半分（6列ずつ） -->
      <BaseCol cols="6">
        <div class="box">左半分</div>
      </BaseCol>
      <BaseCol cols="6">
        <div class="box">右半分</div>
      </BaseCol>
    </BaseRow>
    
    <BaseRow>
      <!-- 画面を3等分（4列ずつ） -->
      <BaseCol cols="4">
        <div class="box">1/3</div>
      </BaseCol>
      <BaseCol cols="4">
        <div class="box">1/3</div>
      </BaseCol>
      <BaseCol cols="4">
        <div class="box">1/3</div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>

<style scoped>
.box {
  background: #f0f0f0;
  padding: 16px;
  text-align: center;
}
</style>
```

## レスポンシブデザイン

画面サイズに応じて、列の幅を変えることができます。

### ブレークポイント

Vuetifyには以下のブレークポイントがあります：

| ブレークポイント | 幅 | 説明 |
|----------------|-----|------|
| xs | < 600px | スマートフォン |
| sm | 600px ~ 960px | タブレット（縦） |
| md | 960px ~ 1264px | タブレット（横）・小さいPC |
| lg | 1264px ~ 1904px | PC |
| xl | ≥ 1904px | 大きいPC |

### レスポンシブな指定

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <!-- 
        xs: スマートフォンでは12列（全幅）
        md: タブレット以上では6列（半分）
        lg: PCでは4列（3分の1）
      -->
      <BaseCol cols="12" md="6" lg="4">
        <div class="box">カード1</div>
      </BaseCol>
      <BaseCol cols="12" md="6" lg="4">
        <div class="box">カード2</div>
      </BaseCol>
      <BaseCol cols="12" md="6" lg="4">
        <div class="box">カード3</div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>
```

この例では：
- **スマートフォン**: 各カードが縦に並ぶ（12列 = 全幅）
- **タブレット**: 各カードが横に2つ並ぶ（6列 = 半分）
- **PC**: 各カードが横に3つ並ぶ（4列 = 3分の1）

## よく使うレイアウトパターン

### 1. 2カラムレイアウト

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <!-- サイドバー -->
      <BaseCol cols="12" md="3">
        <div class="sidebar">サイドバー</div>
      </BaseCol>
      
      <!-- メインコンテンツ -->
      <BaseCol cols="12" md="9">
        <div class="main">メインコンテンツ</div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>
```

### 2. 3カラムレイアウト

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <BaseCol cols="12" md="4">
        <div class="column">カラム1</div>
      </BaseCol>
      <BaseCol cols="12" md="4">
        <div class="column">カラム2</div>
      </BaseCol>
      <BaseCol cols="12" md="4">
        <div class="column">カラム3</div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>
```

### 3. カードグリッド

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <BaseCol
        v-for="item in items"
        :key="item.id"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
        <div class="card">
          <h3>{{ item.title }}</h3>
          <p>{{ item.description }}</p>
        </div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const items = ref([
  { id: 1, title: 'カード1', description: '説明1' },
  { id: 2, title: 'カード2', description: '説明2' },
  { id: 3, title: 'カード3', description: '説明3' },
  { id: 4, title: 'カード4', description: '説明4' }
])
</script>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 16px;
  border-radius: 4px;
  background: white;
}
</style>
```

## 間隔の調整

### 列の間隔（gutter）

`BaseRow`のプロパティで、列の間隔を調整できます。

```vue
<template>
  <!-- 間隔なし -->
  <BaseRow no-gutters>
    <BaseCol cols="6">...</BaseCol>
    <BaseCol cols="6">...</BaseCol>
  </BaseRow>
  
  <!-- 間隔あり（デフォルト） -->
  <BaseRow>
    <BaseCol cols="6">...</BaseCol>
    <BaseCol cols="6">...</BaseCol>
  </BaseRow>
</template>
```

### 列の配置

`BaseRow`のプロパティで、列の配置を調整できます。

```vue
<template>
  <!-- 中央揃え -->
  <BaseRow justify="center">
    <BaseCol cols="6">...</BaseCol>
  </BaseRow>
  
  <!-- 右揃え -->
  <BaseRow justify="end">
    <BaseCol cols="6">...</BaseCol>
  </BaseRow>
  
  <!-- 均等配置 -->
  <BaseRow justify="space-between">
    <BaseCol cols="4">...</BaseCol>
    <BaseCol cols="4">...</BaseCol>
  </BaseRow>
</template>
```

## オフセット

列の前に空白を追加できます。

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <!-- 3列分の空白の後、6列のコンテンツ -->
      <BaseCol cols="6" offset-md="3">
        <div class="box">中央に配置</div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>
```

## 実践的な例

### 商品一覧ページのレイアウト

```vue
<template>
  <BaseGrid>
    <!-- ヘッダー -->
    <BaseRow>
      <BaseCol cols="12">
        <h1>商品一覧</h1>
      </BaseCol>
    </BaseRow>
    
    <!-- フィルターと商品一覧 -->
    <BaseRow>
      <!-- フィルター（サイドバー） -->
      <BaseCol cols="12" md="3">
        <div class="card">
          <h2>フィルター</h2>
          <!-- フィルターの内容 -->
        </div>
      </BaseCol>
      
      <!-- 商品一覧 -->
      <BaseCol cols="12" md="9">
        <BaseRow>
          <BaseCol
            v-for="product in products"
            :key="product.id"
            cols="12"
            sm="6"
            lg="4"
          >
            <div class="card">
              <img :src="product.image" alt="product.name" />
              <h3>{{ product.name }}</h3>
              <p>{{ product.description }}</p>
              <p class="price">{{ product.price }}円</p>
            </div>
          </BaseCol>
        </BaseRow>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 16px;
  border-radius: 4px;
  background: white;
}

.price {
  font-size: 1.25rem;
  font-weight: bold;
}
</style>
```

### ダッシュボードのレイアウト

```vue
<template>
  <BaseGrid>
    <BaseRow>
      <!-- 統計カード -->
      <BaseCol cols="12" sm="6" md="3">
        <div class="card">
          <h3>総売上</h3>
          <p class="stat-value">¥1,000,000</p>
        </div>
      </BaseCol>
      <BaseCol cols="12" sm="6" md="3">
        <div class="card">
          <h3>注文数</h3>
          <p class="stat-value">150</p>
        </div>
      </BaseCol>
      <BaseCol cols="12" sm="6" md="3">
        <div class="card">
          <h3>ユーザー数</h3>
          <p class="stat-value">1,234</p>
        </div>
      </BaseCol>
      <BaseCol cols="12" sm="6" md="3">
        <div class="card">
          <h3>商品数</h3>
          <p class="stat-value">567</p>
        </div>
      </BaseCol>
    </BaseRow>
    
    <!-- グラフとテーブル -->
    <BaseRow>
      <BaseCol cols="12" md="8">
        <div class="card">
          <h3>売上グラフ</h3>
          <!-- グラフの内容 -->
        </div>
      </BaseCol>
      <BaseCol cols="12" md="4">
        <div class="card">
          <h3>最近の注文</h3>
          <!-- テーブルの内容 -->
        </div>
      </BaseCol>
    </BaseRow>
  </BaseGrid>
</template>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 16px;
  border-radius: 4px;
  background: white;
}

.stat-value {
  font-size: 2rem;
  font-weight: bold;
  margin-top: 8px;
}
</style>
```

## まとめ

- **グリッドシステム**: 画面を12列に分割して要素を配置
- **BaseGrid**: コンテナ（最大幅を制限）
- **BaseRow**: 行（横方向のグループ）
- **BaseCol**: 列（実際の要素を配置）
- **レスポンシブ**: 画面サイズに応じて列の幅を変更
- **ブレークポイント**: xs, sm, md, lg, xl

## 参考資料

- [Vuetify - グリッドシステム](https://vuetifyjs.com/ja/components/grids/#section-4f7f304465b9)
- [Vuetify - ブレークポイント](https://vuetifyjs.com/ja/features/breakpoints/)

